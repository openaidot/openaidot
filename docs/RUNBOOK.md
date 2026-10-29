# RUNBOOK — 接手操作

所有命令只读优先；任何写操作先备份、先 dry-run、单独报备。

## 0. 连接

```bash
# root break-glass（只读核验用；不要打印 key）
ssh -i ~/.ssh/<KEY_FILE> root@<VPS_IP>

# 日常账号（VPS 上）
ssh -i /home/dotdeploy/.ssh/id_ed25519 dotdeploy@<VPS_IP>
```

VPS 是 key-only，无密码。

## 1. 只读验收（每次接手先跑）

```bash
hostname; date -u +%FT%TZ
systemctl is-active dwgx-hub-r03d-api dwgx-hub-r03d-worker dwgx-hub-r03d-qq-adapter dwgx-napcat-ws-proxy
curl -s http://127.0.0.1:8766/health; echo
curl -s http://127.0.0.1:3011/health; echo
ss -ltn | grep -E ':(3001|3002|8766|3011)\b'
docker port napcat
journalctl -u dwgx-napcat-ws-proxy -n 20 --no-pager
```

MCP（token 从文件读，不回显）：

```bash
TOK=$(cat /var/lib/dwgx-capability-hub-r03d/secrets/mcp-token)
call() { curl -s -X POST "http://127.0.0.1:8766/mcp?token=$TOK" -H 'Content-Type: application/json' -d "$1"; }
call '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'   | python3 -c 'import sys,json;print(len(json.load(sys.stdin)["result"]["tools"]))'
call '{"jsonrpc":"2.0","id":1,"method":"events/list"}'  | python3 -c 'import sys,json;print(len(json.load(sys.stdin)["result"]["events"]))'
call '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"qq_get_connection_state","arguments":{}}}'
```

DB（只读）：

```bash
python3 - /var/lib/dwgx-capability-hub-r03d/db/hub.sqlite <<'PY'
import sqlite3,sys
c=sqlite3.connect(f"file:{sys.argv[1]}?mode=ro",uri=True)
print("msgs", c.execute("select count(*),max(time) from qq_messages").fetchone())
print("out",  c.execute("select count(*),max(at) from qq_outbound").fetchone())
PY
```

## 2. QQ 恢复（NapCat 重新登录）

现状：adapter down；容器 netns 内 3001 无监听；NapCat 账号已离线，停在 QR 扫码。
**不要**去改 ws-proxy 的 `::1` 目标——proxy 跑在容器 netns 内，改 host 映射无效。

步骤（扫码必须 Owner 本人做）：

1. 看二维码：`docker logs napcat --tail 50`，或打开 NapCat WebUI（`127.0.0.1:6099`，token 在容器配置里，不要打印）。
2. Owner 用手机 QQ 扫码登录账号 `<BOT_QQ>`。
3. 等 NapCat 起 OneBot WS，然后确认容器内监听：

```bash
nsenter -t $(docker inspect -f '{{.State.Pid}}' napcat) -n ss -ltn | grep 3001
```

4. 确认 adapter 自动重连（ws-proxy 每 30s 重试）：

```bash
journalctl -u dwgx-napcat-ws-proxy -n 5 --no-pager   # 不再 ECONNREFUSED
curl -s http://127.0.0.1:3011/health                  # connected:true
```

5. 让群友发一条普通消息，确认 `qq_messages.max(time)` 增长；不要发测试消息。

若登录后 3001 仍无监听：查 NapCat OneBot 配置是否启用 WS server（端口 3001），再看容器日志；
若只绑 `0.0.0.0` 而 `[::1]:3001` 仍拒，才需要处理 proxy 的 `::1` 目标。

## 3. 部署 / 回滚（canonical）

```bash
# 部署（在 VPS 上，由 root runner 执行；先 dry-run）
dwgx-hub-deploy --generation r03d --dry-run
dwgx-hub-deploy --generation r03d

# 回滚（默认只回代码，DB 迁移不逆向）
dwgx-hub-rollback --list
dwgx-hub-rollback --to <ref> --dry-run
```

- 入口脚本：`work/r03-canonical-20261020/scripts/dot-deploy.sh`、`dot-rollback.sh`（untracked）。
- 部署前必须 `git status` 干净：部署 rsync 的是工作区，不是 HEAD。
- 信任锚：`/usr/local/sbin/dwgx-hub-deploy-runner` 必须 root 手动装，不随 tree 部署。
- 一台机器同时只跑一代；r03b 保留为回滚目标。
- 部署会重启 API，MCP 调用会断；断线后查 `hub_status.last_deploy.exit_code`，不要盲目重发。

## 4. Dot 侧

- 只操作 `https://chatgpt.com/dots/home`；代发必须标「非 Owner 发送」。
- 不要给 Dot 开 subagents。
- 群边界：单群 <GROUP_ID>，@ 才主动回复；普通消息只做有限上下文。
- 不在群里发测试消息/日志；私聊测试用 Owner `<OWNER_QQ>`。

## 5. 备份

- R03D cutover 备份：`/var/backups/dwgx/r03d-inplace-20261020T185150Z`
- R03E 备份：`/var/backups/dwgx/r03d-1792607042.tgz`
- 安全热修备份：`/var/backups/dwgx/server.mjs.before-{secfix,openlock,deployarm,readfix}-2026102x`
- HMC 每日 03:30 拉取：`/srv/backups/tokyo-vps/`（14 天）
- 不可删除：`/opt/qq-dot-stack/{data,ntqq,napcat/config}`、`/etc/letsencrypt`、`/etc/wireguard`、`/usr/local/etc/xray`、`<SECRET_PATH>

## 6. 陷阱

- `dwgx-hubctl` 是 r03b 旧包装器（8765/3010），不要用它判断 r03d 状态。
- `hub_status.journal` 过滤 error priority，可能显示 `-- No entries --`；用 `journalctl`。
- `/opt/dwgx-capability-hub-r03d/%HUB_DATA%` 是历史未展开变量残留，部署脚本已排除。
