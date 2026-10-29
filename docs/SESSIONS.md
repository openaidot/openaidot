# SESSIONS — omp 会话地图

5 个「管家」会话共享前缀（前约 3,039 行），不能相加；新增内容从各自 3040 行附近开始。

## 01a15a60 — 历史考古 / HMC / R03D 首次上线

- 时间：2026-10-20T17:34:16Z → 19:29:25Z
- 任务链：五路审计 → HMC 动态出口修复 → R03D 本地验收 + VPS 上线 → QQ 出站 → Dot 交接包
- 产出：
  - `~/.dwgx/tmp/omp-ops-20261014/ALIGN-hmc-vps-dot-20261021.md`
  - `~/.dwgx/tmp/omp-ops-20261014/deploy-r03d-inplace.sh`
  - `~/.dwgx/tmp/omp-ops-20261014/setup-dotdeploy.sh`
  - VPS `/home/dotdeploy/README-DOT.md`
  - 桌面 `dot-handoff-2026-10-21/`（桌面路径现已不存在；vault 有副本）
- 状态：R03D 上线，Dot 直连 22/443 `ENETUNREACH`；HMC P0/凭据轮换未收口。

## 01a15f09 — Dot 初次接入 / origin 收口

- 时间：2026-10-21T15:16:35Z → 19:20:50Z（dispose 19:25:05Z）
- 任务链：Dot 接入包 → CF/本机中继使 Dot 首次发群 → origin 六车道收口/push → 20 项运维审计
- 产出：
  - `OPS/dot-connect/dot_connect.py`、`DOT-PROMPT.md`
  - `OPS/artifacts/dot-connect-261022.zip`
  - `OPS/dot-connect/SUCCESS.md`
  - `OPS/knowledge/{genesis-final-report,audit-ALL,ops-task-ledger}-1022.md`
  - `OPS/knowledge/CODEX-BRIEF.md`
- 状态：Dot 首发「Dot 已接管」成功；依赖临时 CF 通道；origin push 完成但 CI/站点欠账。

## 01a15f83 — HMC/SKIAPI fork（含 Dot R03B 接线）

- 时间：2026-10-21T17:30:20Z → 10-22T15:06:50Z
- 任务链：Dot R03B 接线首发 → SKIAPI/HMC 14 面巡检 → 池/出口审计 → hub 自动更新 → 断流修复
- 产出：
  - `OPS/knowledge/skiapi-hmc-handoff-1022.md`
  - `OPS/knowledge/hmc-inspection-1022.md`
  - `OPS/knowledge/hub-design-1022.md`
  - `OPS/knowledge/webshare-api-1022.md`
- 状态：Dot 接入真实；SKIAPI 池为单色读数；12 路注册因过载全部停止。

## 01a15f9e — DOTBOT 主运维（R03D→R03F）

- 时间：2026-10-21T18:00:06Z → 10-23T04:30:26Z（dispose 04:33:16Z）
- 任务链：进群校准 → R03D cutover → R03E TTS/媒体 → 47/50 工具与自迭代 → Modern MCP 插件 + `hub_call` → `hub.event` 真群唤醒 → 历史导入/Obsidian → 安全五洞修复 → 记忆协议 → 人格校准
- 产出：
  - `OPS/knowledge/DOTBOT-CALIBRATION-20261022.md`
  - `OPS/knowledge/MCP-PROTOCOL-20261022.md`
  - `OPS/knowledge/DOT-MCP-PITFALLS-20261022.md`
  - `OPS/knowledge/DOT-HUB-FINAL-20261022.md`
  - `OPS/knowledge/HUB-SECURITY-REVIEW-20261022.md`
  - `OPS/knowledge/OBSIDIAN-MEMORY-GUIDE-20261022.md`
  - `OPS/research/dot-hub-full-access/`
  - `OPS/research/dot-review-20261023/`（4 份）
  - `OPS/research/dot-memory-skill-prompts/`
  - `OPS/research/dot-deai-persona/`
- 状态：能力层与 5 个安全洞已修；待 Owner 决定是否发「仅闲聊」人格修正版；Dot 侧状态未复核。

## 01a15de6 — 管家知识库工厂

- 时间：2026-10-21T09:59:35Z → 10-26T07:13:17Z（SIGHUP）
- 规模：4,137 次工具调用、292 个子代理任务、141 次真实写入、+4,973/−271 行
- 产出：
  - `OPS/knowledge/{FINAL-REPORT,coverage/,PROJECT-REPORTS,T0-INDEX}.md`
  - `~/.dwgx/BUTLER-KB/`（535 notes / 2,499 links / 0 broken）
  - `OPS/artifacts/full-evidence-20261026/`（70,756 文档逐文件 stat+SHA-256）
  - `OPS/research/owner-semantics-portable/`
- 状态：索引/考古层完成；安全收口 NO-GO（P0 凭据未轮换）；SIGHUP 结束，不是正常完成。
- 独立取证：`WORK/session-forensics-01a15de6/reports/00-MASTER.md`（结论：高质量自治工程，但不是它宣称的 100% 收口）。

## 01a17851 — 深读 / 架构层 / 取证

- 时间：2026-10-26T13:06:20Z → 10-28T05:58:32Z（abort）
- 产出：
  - `WORK/session-forensics-01a15de6/`
  - `WORK/butler-prompt-audit/`
  - `OPS/artifacts/deepread-20261027/`（49 卡、46 项目深读、1,714 docs、9,565 claims）
  - `BUTLER-KB/15-Architecture/`
- 状态：16 deepread-pass / 30 blocked / 3 Owner 排除；结束时 G10skiapi 仍 running，wave-10 未收口。

## 读法

1. 先读 `01a15f9e` 的 `dot-review-20261023/REVIEW.md` §6（已修）再回看 §3（风险）。
2. 会话原文只读定向窗口，不整份通读；引用带 `文件:行号`。
3. `01a15de6` 的前缀被三个 fork 共享，统计时勿重复计数。
