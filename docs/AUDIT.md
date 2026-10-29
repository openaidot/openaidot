# Report — chatgptdot-omp-ops-artifacts

## Answer

omp 为 ChatGPT Dot 做的运维产物集中在四处：`~/.dwgx/ops/knowledge`（结论文档 + 六份 Dot 部署报告 + 安全审查）、`~/.dwgx/ops/research/dot-*`（审计/记忆/人格研究）、`~/.dwgx/work/r03-canonical-20261020`（唯一代码树，HEAD `c5117d1`=R03F）加 `work/dot-mcp-hub`（公开手册/插件包）、`E:/dwgxhomecloud-vault/vault/dot-handoff-2026-10-21`（接入说明 + 两把私钥）。会话内容分布在 5 个「管家」会话，Dot 运维主会话是 `01a15f9e`，代码/知识库主线是 `01a15de6`（C014, S31, S32, S34, S35）。

截至 2026-10-29T00:10Z 线上：东京 VPS `<VPS_IP>` 的 r03d 三 unit 全 active，Hub `0.3.0`，`tools/list=50`、`events/list=7`；**但 QQ adapter 是 down/connected:false，群消息自 2026-10-26T01:57:52Z 起停止入站**。验证器复核后根因不是 ws-proxy 的 `::1:3001` 目标（proxy 用 nsenter 跑在容器 netns 内），而是 **NapCat 的 QQ 会话已登出、停在扫码界面，OneBot WS 3001 不再监听**（C003 已标 contradicted，C023）。

## 接手清单（四层）

### A. 运行面（线上，2026-10-29 实测）

| 项 | 值 | 证据 |
|---|---|---|
| 主机 | `<VPS_HOST>` = `<VPS_IP>`（Rocky 9.8） | S39 |
| Hub units | `dwgx-hub-r03d-api / -worker / -qq-adapter` 全 active | S39 |
| Hub 健康 | `{"ok":true,"version":"0.3.0"}`，端口 8766；adapter 3011 | S39 |
| 工具/事件 | 50 tools / 7 events | S39 |
| QQ | adapter `status:down, connected:false`；NapCat 容器 Up 9 天 | S39, S41 |
| 断链点 | ws-proxy 日志 `relay: connect ECONNREFUSED ::1:3001`；容器 netns 内 3001 无监听（IPv4/IPv6 都拒） | S41, C023 |
| 根因 | NapCat 的 QQ 核心已登出，停在 QR 扫码；登录错误 10-26 157 / 10-27 715 / 10-28 229 次，最后 2026-10-28T07:43:50Z | C023 |
| 最后入站/出站/事件 | 入站 2026-10-26T01:57:52Z；出站 2026-10-25T17:09:35Z；事件投递 2026-10-25T19:58:19Z | C004 |
| 数据量 | qq_messages 4,886；qq_outbound 172；event_deliveries 2,374；subscriptions 6；audit_log 11,332 | C006 |
| 仍在跑的定时任务 | `tibo-watch.sh` 每 10 分钟；群历史导入每日 04:15 | S44 |
| 陷阱 | `/usr/local/sbin/dwgx-hubctl` 仍指向 r03b units 与 8765/3010，在 r03d 上打印 inactive | C005, S40 |
| 回滚目标 | r03b 树/data/env 保留；R03D cutover 备份 `/var/backups/dwgx/r03d-inplace-20261020T185150Z` | S10, S19 |

### B. 代码面（本地）

| 项 | 值 | 证据 |
|---|---|---|
| canonical 树 | `~/.dwgx/work/r03-canonical-20261020`，HEAD `c5117d1`（R03F）；工作树 23 项脏（9 M + 14 ??） | S25, S9, S6, C024 |
| 线上代码 | **不等于 `c5117d1`**：live `server.mjs` sha256 `407df521…` vs HEAD blob `3ee81b92…`（442 行差异）；live `adapter.mjs` 与工作区未提交版本逐字节相同；live 多 65 行 deploy-arm 门、少 10 行 | C024 |
| 部署入口 | `scripts/dot-deploy.sh` / `dot-rollback.sh`（untracked，随 R03G 工作树） | S14, S25 |
| 可直接部署包 | **没有**：R03D src tgz 是旧代；canonical 含未提交文件；线上已从脏工作区部署并继续漂移 | S10, S14, C024 |
| 历史包 | `r03b-current-source{,2,3}.tgz`（R03B 快照）、`r03d-src.tgz`（R03D）、`r03b-return/`（实为 R03C return） | S8, S10 |
| 公开包 | `~/.dwgx/work/dot-mcp-hub`（README/docs/plugin，无服务端实现） | S24 |
| 部署证据 | 六份 `Dot*REPORT.md`：Cutover 9 PASS；R03E 128/128；R03F 142/142；SelfDeploy 160/160；TTS 128/127/1 | S10–S15 |

### C. 记忆面（知识库与交接）

| 项 | 值 | 证据 |
|---|---|---|
| BUTLER-KB | 535 notes / 2,499 links / 0 broken（10-26 复跑） | C013, S31, S32 |
| dotbot 卡 | `ops/atlas/projects/dotbot/CARD.md`（state_as_of 2026-10-26T01:19+09:00；partial；唯一 open=TTS Owner 回执） | S33 |
| 需求账 | `atlas/projects/dotbot/LEDGER.tsv` R-DOT-001…013（含 4 条红线） | S33 |
| 架构卡 | `BUTLER-KB/15-Architecture/ARCH-dotbot.md`（deepread 10 docs / 50 claims / gate PASS） | S29, S30 |
| Obsidian 指南 | 目录/frontmatter/写入纪律（给 Dot 的记忆协议） | S21 |
| 研究包 | `research/dot-deai-persona`（2,767 条群消息人格测量）、`dot-memory-skill-prompts`（三条 prompt）、`dot-hub-full-access`（能力边界） | S6, OpsMap 切片 |
| vault 交接 | `dot-handoff-2026-10-21/`：DOT-ACCESS-HANDOFF、DOT-CONTINUE-PROMPT、两把私钥（只记位置） | S19, S20, C011 |
| Dot 自己的库 | 自报 `/workspace/shared/obsidian/vault`；本机无副本，哈希未证 | SessionMap 切片；C018 同类 |
| 敏感包 | `ops/artifacts/dot-connect-261022.zip`（4,167 B，含 key+token） | S18, C012 |

### D. 会话面（5 个管家会话）

| 会话 | 时间 | 内容 | 证据 |
|---|---|---|---|
| `01a15a60` | 10-20 17:34→19:29Z | HMC 修复、R03D 首次上线、QQ 出站、桌面交接包 | SessionMap 切片 |
| `01a15f09` | 10-21 15:16→19:20Z | Dot 首次接入（CF→本机→VPS），origin 收口 | 同上 |
| `01a15f83` | 10-21 17:30→10-22 15:06Z | Dot R03B 接线首发 + SKIAPI/HMC 巡检 | 同上 |
| `01a15f9e` | 10-21 18:00→10-23 04:30Z | **Dot 运维主会话**：R03D→R03F、50 工具、安全五洞、记忆/人格 | C014, S34 |
| `01a15de6` | 10-21 09:59→10-26 07:13Z | 管家知识库工厂（BUTLER-KB、70,756 文档、1,709 会话），SIGHUP 结束 | C014, C015, S31, S35 |
| `01a17851` | 10-26 13:06→10-28 05:58Z | 深读/架构层/取证；abort 结束 | S38, S31 |

### E. 待办与风险（接手第一周）

1. **QQ 断链**（当前 P0）：ws-proxy 连 `::1:3001`，NapCat 只监听 `127.0.0.1:3001`；修复前群消息不会进来（C003）。
2. **R03F 未 cutover**：线上仍是 r03d 槽位 + 自迭代 overlay；R03F 只在 canonical/报告里（C007, S33）。
3. **TTS 验收**：send 侧已有 4 次 record（最新 974493014 @2026-10-24T20:02Z），Owner 未回执（C019）。
4. **安全债**：root deploy 有 deploy-arm 门（10-23 加），但 `hub_call` 仍可到 `hub_write_file/hub_deploy`；匿名读已关（C008, C009, C021）。
5. **凭据**：`dot-connect-261022.zip`、`work/create-final*.js`、`fill-plugin-form.js` 有明文 token/key（C012；S6 I12）。
6. **文档漂移**：`T0-261022`/`T0-LIVE-STATUS` 同文自相矛盾；`DOT-HUB-FINAL` 表 50/192 vs 块 47/184；`hubctl` 指向旧代（C005, C020；S8, S9）。
7. **未提交工作树**：R03G/部署脚本/自迭代改动只在工作区；重建包前先固定（S25, S14）。

## Confidence

**High**（A 级为主）：产物路径、canonical HEAD、线上 unit/工具数、QQ down、NapCat 登出与 3001 无监听、数据量、vault 密钥位置、5 个会话的身份。
**Medium**（B 级）：六份报告的历史测试数、10-23 修复的独立有效性、BUTLER-KB 计数、Dot 侧 vault/Drive 状态。
**Low**：Dot 云电脑内部状态、R03F 是否应现在 cutover、TTS 是否已被 Owner 实际听到、QQ 登出前的 3001 监听历史（`[UNVERIFIED]`）。

## What would change it

- 扫码登录后 `3001` 仍无监听或 `[::1]:3001` 仍拒，则存在第二缺陷（netns 绑定），根因要改。
- 若出现一份 source manifest 证明 live 文件哈希等于 `c5117d1` blob，则 C024 被推翻。
- 若 `hub_status.last_deploy` 的 generation/工具面证明 R03F 已 cutover，则「线上仍是 r03d 槽位」要改写。
- 若 Owner 已确认 TTS，则唯一 open item 关闭。
- 若 Dot 的 `/workspace/shared/obsidian/vault` 有可核哈希/备份，记忆面从 `[UNVERIFIED]` 升 A。

## Open questions

- 谁去扫码恢复 NapCat 登录？这是 Owner 的物理/账号动作，omp 不应代扫。
- R03F/R03G 的部署窗口与回滚演练由谁拍板？
- `dot-connect` 的 key/token 是否已轮换？vault 里两把私钥是否仍在用？
- Dot 的记忆库是否有 Drive 之外的本地副本？`[UNVERIFIED]`。
- 群 <GROUP_ID> 的「@才回复」边界是否仍生效？10-23 后无群流量，无法从数据验证。

## Gap loop

- Round 1（本地 + 线上）：live 探针发现 QQ down、hubctl 旧代、入站停在 10-26；新增 C002–C006、S39–S42。非空。
- Round 2（post-10-23 产物）：找到 `session-forensics-01a15de6`、`butler-prompt-audit`、`deepread-20261027`、`atlas CARD`；新增 C013–C021、S31–S33。非空。
- Round 3（verification）：HubVerify 推翻 `::1` 根因、发现 live≠HEAD；新增 C023–C024，C003 改 contradicted。非空。
- Stop reason：**budget（45 min / 60 sources）**；没有出现连续两个空轮。未跑第四轮（NapCat 扫码与 R03F cutover 需要 Owner 动作）。

## Verification

- Verifier：`HubVerify`（独立子代理，2026-10-29T00:31Z，只读）。
- Claim 1（QQ 断链及根因）：**adapter down = VERIFIED；`::1:3001` 是根因 = REFUTED**。复核方法：ssh 只读、`systemctl is-active`、adapter `/health`、`nsenter -t <napcat pid> -n ss -ltn`、容器内 `curl 127.0.0.1:3001` 与 `[::1]:3001`、`docker logs napcat`、`journalctl`、sqlite `max(time)`。反例：proxy 用 nsenter 跑在容器 netns 内，容器内 3001 无监听；NapCat 日志显示账号离线并停在 QR 扫码（ErrType 1 ErrCode 3）。修正为 C023。
- Claim 2（canonical HEAD `c5117d1`、线上 r03d）：**字面 = VERIFIED**（`git log -1`、`git status --porcelain` 23 项、unit 名 r03d）；**「live == HEAD」= REFUTED**：live `server.mjs` sha256 `407df521…` vs HEAD blob `3ee81b92…`（442 行差异），live `adapter.mjs` 与工作区未提交版本逐字节相同，live 多 65 行 deploy-arm 门、少 10 行。修正为 C024。
- 无法核验：`ECONNREFUSED` 在 2026-10-27T12:59Z 之前的起始时间（journal 只保留本次 boot）；10-26 02:54→10-27 12:59 之间 3001 是否曾监听。上述时间线标 `[UNVERIFIED]`。
- 其余 A 级断言（路径、数据量、vault 密钥位置、会话身份）由本轮直接命令复核，未逐条交 verifier。
