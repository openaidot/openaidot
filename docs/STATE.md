# STATE — 当前状态（2026-10-29T00:10Z 实测）

## 线上

| 项 | 值 | 证据 |
|---|---|---|
| 主机 | `<VPS_HOST>` / `<VPS_IP>`（Rocky 9.8, node v22.20.0） | ssh probe 2026-10-29T00:10:40Z |
| Hub units | `dwgx-hub-r03d-api` / `-worker` / `-qq-adapter` 全 active | 同上 |
| Hub health | `{"ok":true,"service":"dwgx-capability-hub","version":"0.3.0"}` @ 8766 | 同上 |
| MCP | `tools/list=50`，`events/list=7` | 同上 |
| QQ adapter | `{"status":"down","connected":false,"ws":"ws://127.0.0.1:3002","login_user":<BOT_QQ>}` | `qq_get_connection_state` 2026-10-29T00:10:41Z |
| NapCat | 容器 `napcat` Up 9 days；端口映射 `127.0.0.1:3001`、`127.0.0.1:6099` | `docker ps` / `docker port napcat` |
| ws-proxy | active，但每 30s：`relay: connect ECONNREFUSED ::1:3001` | `journalctl -u dwgx-napcat-ws-proxy` 00:10:47Z |
| 最后入站 | `qq_messages.max(time)=1792979872` → 2026-10-26T01:57:52Z | hub.sqlite 只读 |
| 最后出站 | 2026-10-25T17:09:35Z | `qq_outbound.max(at)` |
| 最后事件投递 | 2026-10-25T19:58:19Z | `event_deliveries.max(created_at)` |
| 数据量 | qq_messages 4,886；qq_outbound 172；event_deliveries 2,374；subscriptions 6；audit_log 11,332；jobs 12 | hub.sqlite |
| 定时任务 | `*/10 * * * * tibo-watch.sh`；每日 04:15 群历史导入 | VPS crontab |

### 断链诊断（2026-10-29 复核后）

```
adapter 连:   ws://127.0.0.1:3002 → ws-proxy
ws-proxy:     用 nsenter 跑在 NapCat 容器 netns 内，relay 目标 ::1:3001
容器 netns:   ss -ltn 只有 0.0.0.0:6099；3001 无监听（IPv4/IPv6 都 refused）
NapCat:       账号已离线，停在 QR 扫码；Login Error ErrType:1 ErrCode:3
              10-26 157 次 / 10-27 715 次 / 10-28 229 次，最后 2026-10-28T07:43:50Z
```

结论：**不是 `::1` 绑定问题**（proxy 在容器内，host 的 127.0.0.1 映射与它无关）。
根因是 QQ 会话登出，NapCat 的 OneBot WS 服务没起来。恢复动作是 Owner 扫码登录，不是改 proxy 目标。
登录后仍需复核 `3001` 是否在容器内监听；若只绑 `0.0.0.0` 而 `[::1]` 仍拒，才是第二缺陷。

## 本地

| 项 | 值 |
|---|---|
| canonical 树 | `<HOME>/.dwgx/work/r03-canonical-20261020` |
| HEAD | `c5117d1 r03f: artifact_upload and qq_send_media for QQ media delivery` |
| 链 | `13044dc r03c` → `c4bf411 r03d` → `ef966a1 r03e` → `c5117d1 r03f` |
| 工作树 | 未提交 R03G/自迭代/出站扩展（10-26 卡面记 23 项） |
| 部署入口 | `scripts/dot-deploy.sh` / `dot-rollback.sh`（untracked） |
| 线上代际 | r03d 槽位；R03F 未 cutover（unit 名仍 r03d） |
| 线上代码 | **不等于 HEAD**：live `server.mjs` sha256 `407df521…` vs HEAD blob `3ee81b92…`（442 行差）；live `adapter.mjs` 与工作区未提交版逐字节相同；live 多 65 行 deploy-arm 门、少 10 行 |
| 交接卡 | `ops/atlas/projects/dotbot/CARD.md`（state_as_of 2026-10-26T01:19+09:00，partial） |

## 数字口径

- 测试数随代际变化：96（R03D cutover）→ 128（R03E）→ 142（R03F）→ 160（SelfDeploy）→ 192/193（10-23 安全修复后）。
- 消息数随时间增长：1,530（10-21 校准）→ 2,514/2,518（10-22 回填）→ 2,727/2,729（10-23 审计）→ 4,677（10-26 卡）→ 4,886（10-29 实测）。
- 工具数：35（10-20）→ 44/46/47/49（10-22 演进）→ 50（10-22 17:20 之后，至 10-29 未变）。
- 任何单一数字都必须带日期；跨文档直接比较视为缺陷。
