# INVENTORY — 产物清单

根：`OPS=~/.dwgx/ops`、`WORK=~/.dwgx/work`、`VAULT=E:/dwgxhomecloud-vault/vault`。

## 1. 结论/交接文档（必读）

| 路径 | 日期 | 内容 | 状态 |
|---|---|---|---|
| `OPS/knowledge/DOT-HUB-FINAL-20261022.md` | 10-22 | 最终 50 tools / 7 events / 自迭代 / 出站对账 | 主入口；数字块有内部矛盾 |
| `OPS/knowledge/MCP-PROTOCOL-20261022.md` | 10-22 | 端点契约、三代协议、事件、安全姿态 | 可用 |
| `OPS/knowledge/DOT-MCP-PITFALLS-20261022.md` | 10-22 | 给 ChatGPT dots 接 MCP 的七条坑（可公开） | 已发 gist |
| `OPS/knowledge/HUB-SECURITY-REVIEW-20261022.md` | 10-22 | C1/C2/H1 发现与修复 | 可用 |
| `OPS/knowledge/DOTBOT-CALIBRATION-20261022.md` | 10-22 | 进群基线：9 问题、P0–P2 | 历史起点 |
| `OPS/knowledge/OBSIDIAN-MEMORY-GUIDE-20261022.md` | 10-22 | 给 Dot 的 vault 结构/frontmatter | 设计基线 |
| `OPS/research/dot-review-20261023/REVIEW.md` | 10-23 | 8 条 Owner 需求、能力/风险/修复 | 先读 §6 再读 §3 |
| `OPS/research/dot-review-20261023/HUB-AUDIT.md` | 10-23 | A1–A16/B1–B7/C1–C11 + R1–R8 | 修复前快照 |
| `OPS/research/dot-review-20261023/LEDGER.md` | 10-23 | 78 行时间线、D1–D13、I1–I14、U1–U16 | 追历史首选 |
| `OPS/research/dot-review-20261023/DOT-CONVERSATION.md` | 10-23 | dots/home 32 条对话摘录 | UI 只能回溯 32 条 |
| `OPS/atlas/projects/dotbot/CARD.md` | 10-26 | 最新项目卡：partial、唯一 open=TTS | 接手状态基准 |
| `OPS/atlas/projects/dotbot/LEDGER.tsv` | 10-22 | R-DOT-001…013 需求/红线 | 需求账 |
| `BUTLER-KB/10-Projects/dotbot.md` | 10-26 | 项目卡（Obsidian 版） | 蒸馏 |
| `BUTLER-KB/15-Architecture/ARCH-dotbot.md` | 10-28 | deepread 10 docs / 50 claims / gate PASS | 架构层 |
| `BUTLER-KB/60-Dotbot/dotbot-SUMMARY.md` | 10-24 | 校准摘要 | 历史 |

## 2. 部署/实现报告（六份，按代际）

| 路径 | 代际 | 结论 | 测试 |
|---|---|---|---|
| `OPS/knowledge/harness/DotCutover-REPORT.md` | R03D | 9 PASS / 0 FAIL / 1 未演练 | 96/96 VPS |
| `OPS/knowledge/harness/DotTTSTool-REPORT.md` | R03E | TTS/预览/转发展开 | 128/127/1 |
| `OPS/knowledge/harness/DotDeployR03E-REPORT.md` | R03E 部署 | live on r03d | 128/128 VPS |
| `OPS/knowledge/harness/DotMediaUpload-REPORT.md` | R03F | artifact_upload + qq_send_media | 142/142 VPS |
| `OPS/knowledge/harness/DotSelfIterate-REPORT.md` | R03G 前身 | 装 deploy/rollback 脚本 | 未真实部署 |
| `OPS/knowledge/harness/DotSelfDeploy-REPORT.md` | 自迭代 | 四工具 + root runner | 160/160 VPS |

## 3. 代码与包

| 路径 | 版本 | 状态 |
|---|---|---|
| `WORK/r03-canonical-20261020` | HEAD `c5117d1` R03F + 23 项未提交 | **唯一 canonical**；线上代码不等于 HEAD；需重建包 |
| `WORK/r03-hub-20261020` / `r03-events-20261020` / `r03-integration-20261020` | `642bd66` / `2590c54` / `642bd66` | 仅证据（已并入 canonical） |
| `WORK/dot-mcp-hub` | plugin 1.0.0 | 公开手册 + portable plugin，无服务端实现 |
| `TMP/r03d-src.tgz` | R03D | 旧代源码包；配 `deploy-r03d-inplace.sh` |
| `TMP/r03b-return/` | 实为 R03C return（`13044dc`） | 完整证据包，非最新代码 |
| `TMP/r03b-current-source{,2,3}.tgz` | R03B 快照 | 仅证据 |
| `WORK/events-r03.patch` / `qq-r03.patch` | P0-A / P0-C | 仅证据 |

**没有“原样可直接部署”的当前包。**

## 4. 接入与凭据（只记位置，不复制内容）

| 路径 | 是什么 |
|---|---|
| `OPS/dot-connect/DOT-PROMPT.md` | 早期给 Dot 的接入 prompt（8765/SSH 转发，已过期） |
| `OPS/dot-connect/{dot_connect.py,dot_min.py,install-tunnel.sh,start-tunnel.ps1,watchdog.ps1}` | 一次性接入/隧道脚本 |
| `OPS/dot-connect/{bundle/,cloudflared.exe,watchdog.log,cf-err.log,current-url.txt}` | 打包副本/日志/临时 URL（敏感） |
| `OPS/artifacts/dot-connect-261022.zip` | 4,167 B，含 `dot_connect_key` + `<TOKEN_FILE>` |
| `VAULT/dot-handoff-2026-10-21/DOT-ACCESS-HANDOFF.md` | RDC→Windows→VPS 接入说明 |
| `VAULT/dot-handoff-2026-10-21/DOT-CONTINUE-PROMPT.md` | Dot 角色/只读验收/边界 |
| `VAULT/dot-handoff-2026-10-21/<KEY_FILE>` | VPS root break-glass（私钥，勿外发） |
| `VAULT/dot-handoff-2026-10-21/<KEY_FILE>` | `dotdeploy` 日常 key（私钥，勿外发） |

## 5. 研究包（设计与测量，不是线上证明）

| 路径 | 结论 |
|---|---|
| `OPS/research/dot-hub-full-access/` | Dot 能力边界：managed shell 有 HTTPS，Cloud Desktop 是另一 netns，无 sudo；事件唤醒已证 |
| `OPS/research/dot-memory-skill-prompts/` | 三条 prompt：记忆协议、skill、浏览；vault 不放 frozen skill |
| `OPS/research/dot-deai-persona/` | 2,767 条群消息测量：长度/多行/句号/报告词/时延；建议先改形态再谈人格 |
| `OPS/artifacts/deepread-20261027/dotbot/` | 10 docs / 50 claims / GATE-02 10 PASS |

## 6. 会话与取证

| 路径 | 内容 |
|---|---|
| `~/.omp/agent/sessions/-.dwgx/*01a15f9e*.jsonl` | Dot 运维主会话（10.9MB） |
| `~/.omp/agent/sessions/-.dwgx/*01a15de6*.jsonl` | 管家知识库工厂（35MB） |
| `WORK/session-forensics-01a15de6/` | 该会话的手术刀报告（20 切片 + 6 专项 + 台账） |
| `WORK/butler-prompt-audit/` | 5 个管家会话的 213 条唯一指令 + 58 条真实性矩阵 |
| `OPS/research/chatgptdot-omp-ops-artifacts/` | 本轮盘点：BRIEF/sources/claims/REPORT + 4 份 slice |

## 7. 旧部署/迁移资料（历史上下文）

| 路径 | 内容 |
|---|---|
| `E:/dwgxdashboard/research/qq-dot-stack/` | 10-20 旧 VPS 的 NapCat/AstrBot/bridge 设计与 runbook |
| `E:/dwgxdashboard/research/migration-tokyo-vps-20261020/` | 旧机→Rocky 9.8 迁移；HMC 每日备份脚本 |
| `VAULT/domains.md`、`vps-tokyo*.md/json` | 网络资产；多数早于迁移，勿当现行 |
