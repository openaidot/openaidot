# SOURCES — 断言与源

完整机器可读清单：`../sources.tsv`、`../claims.tsv`。所有路径以本机为准。

## 线上（2026-10-29 实测）

| ID | 源 | locator |
|---|---|---|
| S39 | VPS 只读探针 | `ssh root@<VPS_IP> < probe-hub-20261029.sh`；输出 2026-10-29T00:10:40Z |
| S40 | `dwgx-hubctl` | `/usr/local/sbin/dwgx-hubctl:3-9` |
| S41 | ws-proxy 日志 | `journalctl -u dwgx-napcat-ws-proxy`（00:10:47Z） |
| S42 | hub.sqlite | `/var/lib/dwgx-capability-hub-r03d/db/hub.sqlite`（只读 count/max） |
| S44 | tibo cron | VPS `crontab -l`：`*/10 * * * * /usr/local/bin/tibo-watch.sh` |

## 结论/审计

| ID | 源 | locator |
|---|---|---|
| S1 | DOT-HUB-FINAL | `OPS/knowledge/DOT-HUB-FINAL-20261022.md` §What is live / §Verified commands |
| S2 | DOT-MCP-PITFALLS | `OPS/knowledge/DOT-MCP-PITFALLS-20261022.md` §1–§7 |
| S3 | DOTBOT-CALIBRATION | `OPS/knowledge/DOTBOT-CALIBRATION-20261022.md` §一/§二 |
| S4 | REVIEW | `OPS/research/dot-review-20261023/REVIEW.md` §1–§7 |
| S5 | HUB-AUDIT | `OPS/research/dot-review-20261023/HUB-AUDIT.md` Headline / Risks |
| S6 | LEDGER | `OPS/research/dot-review-20261023/LEDGER.md` 全表 |
| S7 | DOT-CONVERSATION | `OPS/research/dot-review-20261023/DOT-CONVERSATION.md` 表头 |
| S21 | OBSIDIAN-MEMORY-GUIDE | `OPS/knowledge/OBSIDIAN-MEMORY-GUIDE-20261022.md` §1–§2 |
| S22 | MCP-PROTOCOL | `OPS/knowledge/MCP-PROTOCOL-20261022.md` |
| S23 | HUB-SECURITY-REVIEW | `OPS/knowledge/HUB-SECURITY-REVIEW-20261022.md` |

## 部署报告

| ID | 源 |
|---|---|
| S10 | `OPS/knowledge/harness/DotCutover-REPORT.md` |
| S11 | `OPS/knowledge/harness/DotDeployR03E-REPORT.md` |
| S12 | `OPS/knowledge/harness/DotMediaUpload-REPORT.md` |
| S13 | `OPS/knowledge/harness/DotSelfDeploy-REPORT.md` |
| S14 | `OPS/knowledge/harness/DotSelfIterate-REPORT.md` |
| S15 | `OPS/knowledge/harness/DotTTSTool-REPORT.md` |
| S16 | `OPS/knowledge/harness/CHATGPT-AUDIT-20261021.md` |

## 代码

| ID | 源 |
|---|---|
| S25 | `WORK/r03-canonical-20261020`（`git log -1` = `c5117d1`） |
| — | `WORK/r03-{hub,events,integration}-20261020`（仅证据） |
| — | `TMP/r03d-src.tgz`、`TMP/r03b-return/`、`TMP/r03b-current-source*.tgz` |
| S24 | `WORK/dot-mcp-hub/README.md` |

## 状态卡

| ID | 源 |
|---|---|
| S33 | `OPS/atlas/projects/dotbot/CARD.md` + `LEDGER.tsv` |
| S29/S30 | `BUTLER-KB/10-Projects/dotbot.md`、`15-Architecture/ARCH-dotbot.md` |
| S31 | `WORK/session-forensics-01a15de6/reports/00-MASTER.md` |
| S32 | `WORK/butler-prompt-audit/BUTLER-AUDIT.md` + `BUTLER-TRUTH-MATRIX.tsv` |

## 会话

| ID | 会话 | 路径 |
|---|---|---|
| S34 | 01a15f9e（Dot 主运维） | `~/.omp/agent/sessions/-.dwgx/*01a15f9e*.jsonl` |
| S35 | 01a15de6（KB 工厂） | `~/.omp/agent/sessions/-.dwgx/*01a15de6*.jsonl` |
| S36 | 01a15f09 | `~/.omp/agent/sessions/-.dwgx/*01a15f09*.jsonl` |
| S37 | 01a15f83 | `~/.omp/agent/sessions/-.dwgx/*01a15f83*.jsonl` |
| S38 | 01a17851 | `~/.omp/agent/sessions/-.dwgx/*01a17851*.jsonl` |

## 交接/凭据（只记位置）

| ID | 源 |
|---|---|
| S19 | `VAULT/dot-handoff-2026-10-21/DOT-ACCESS-HANDOFF.md` |
| S20 | `VAULT/dot-handoff-2026-10-21/DOT-CONTINUE-PROMPT.md` |
| S18 | `OPS/artifacts/dot-connect-261022.zip` |
| S17 | `OPS/dot-connect/DOT-PROMPT.md` |

## 复跑

```bash
# 本报告的全部数字
python ~/.omp/agent/skills/deepresearch/scripts/deepresearch_state.py status --slug chatgptdot-omp-ops-artifacts
# 线上
ssh -i ~/.ssh/<KEY_FILE> root@<VPS_IP> < ~/.dwgx/tmp/probe-hub-20261029.sh
```
