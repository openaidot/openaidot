# RISKS — 风险与凭据

## 当前 P0

1. **QQ 链路断开**（2026-10-26T01:57Z 起）：NapCat 的 QQ 会话登出、停在 QR 扫码，OneBot WS 3001 无监听；adapter 因此 down。恢复需 Owner 扫码，见 `RUNBOOK.md` §2。`::1` 不是根因。
2. **root deploy 路径**：`hub_call` 可转发到 `hub_write_file` → `hub_deploy`，最终由 root systemd path unit 执行。10-23 加了 deploy-arm（`/var/lib/dwgx-capability-hub-r03d/secrets/deploy-arm`，root 0600，<30 分钟，成功后删除），但任何能调用 `hub_call` 的路径仍可触达整个工具面。
3. **凭据散落**：
   - `OPS/artifacts/dot-connect-261022.zip` 内含 `dot_connect_key`、`<TOKEN_FILE>`。
   - `WORK/create-final.js`、`create-final2.js`、`fill-plugin-form.js` 含明文 MCP token。
   - `VAULT/dot-handoff-2026-10-21/` 含两把私钥。
   - 处置：只记位置，不复制；优先轮换 token，再决定 key 是否继续使用。

## 审计发现（10-23，含已修）

| 编号 | 风险 | 状态 |
|---|---|---|
| R1 | `/mcp-open` 匿名读 Owner 私聊与全群历史（无 rate limit） | 已修：白名单收紧 + 60 req/min/IP |
| R2 | 群消息提示注入 → `hub_call` → root deploy | 部分：加 deploy-arm 门，未见人工确认环 |
| R3 | `qq_read` 绕过 `QQ_ALLOWED_GROUPS`（可读所有群/好友） | 已修：非白名单 FORBIDDEN |
| R4 | 分页 `time < cursor` 在同秒边界永久漏消息 | 已修：`(time,message_id)` 游标 |
| R5 | stateless 路径绕过 Zod（`limit=-1` 全量拉取） | 已修：handler clamp 1..200 |
| R6 | `hub.event` 订阅钉死 `kind`，新事件 kind 仍要改订阅 | 未修 |
| R7 | 测试套件硬编码 `/opt/dwgx-capability-hub-r03d`，迁移副本会假失败 | 未修 |
| R8 | 文档漂移（47/184/5 vs 50/192/7；r03b 包装器） | 未修 |

## 文档矛盾（引用时必须带日期）

- `T0-261022.md`：同文先写「R03D/E/F 均未部署」，后写「R03D 已部署三 unit active」。
- `T0-LIVE-STATUS.md`：同文写「VPS 仍跑 R03B」「r03d active」「未提交 3」与「3 M + 3 ??」。
- `DOT-HUB-FINAL`：表 50 tools / 192 tests；「Verified commands」块 47 / 184 / 5 events。
- `package.json` 永远是 `0.3.0`，不能用来判别 R03B/C/D/F。

## 未验证（Dot 侧）

- Dot 云电脑 `/workspace/shared/obsidian/vault` 是否存在、是否有本地副本/哈希。
- Dot 是否仍持有并使用 VPS root key。
- 10-23 修复后的真实插件权限（Allow all）与平台唤醒。
- TTS 语音是否被 Owner 实际听到（唯一 open item）。
- R03F 是否已在 10-23 后 cutover（unit 名不变，需看 `last_deploy` 与工具面）。

## 操作红线

- 不在 400 人群 <GROUP_ID> 发测试消息/日志。
- 不给 Dot 开 subagents。
- 不测旧 IP。
- 不往 Owner 的其他 ChatGPT 会话写消息。
- 不把私钥/token 写进仓库、报告、板。
