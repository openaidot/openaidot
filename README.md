# openaidot — ChatGPT Dot 运维接手包

本仓库是 **ChatGPT Dot（dotbot / dwgx的宝宝）** 运维的接手文档，不是产品代码。
代码在 `~/.dwgx/work/r03-canonical-20261020`，线上在东京 VPS `<VPS_IP>`，完整证据在
`~/.dwgx/ops/research/chatgptdot-omp-ops-artifacts/`。

## 三分钟版

- Dot 是 `chatgpt.com/dots/home` 里的自定义 GPT，唯一大脑；QQ 群 <GROUP_ID> 是它的主战场。
- 它的执行面是自建 MCP Hub（r03d，端口 8766）+ NapCat/OneBot adapter（3011）。
- 2026-10-29T00:10Z 实测：Hub 三 unit active、50 tools、7 events；**QQ adapter down**，群消息自 2026-10-26T01:57:52Z 起停止入站。
- 根因：NapCat 的 QQ 会话登出、停在 QR 扫码，容器内 3001 无监听（`::1` 不是根因）；恢复需 Owner 扫码。
- 代码 HEAD `c5117d1`（R03F），工作树 23 项脏；线上代码不等于 HEAD，且 R03F 未 cutover。

## 文档

| 文件 | 内容 |
|---|---|
| `docs/STATE.md` | 当前线上/本地状态、断链诊断、数字口径 |
| `docs/INVENTORY.md` | 全部产物清单（运行/代码/记忆/研究/交付包） |
| `docs/SESSIONS.md` | 6 个 omp 会话的地图与各自产出 |
| `docs/RUNBOOK.md` | 只读核验、QQ 修复路径、部署/回滚入口、操作边界 |
| `docs/RISKS.md` | 安全债、凭据位置、文档漂移 |
| `docs/DECISIONS.md` | 架构决策 D1–D13、事故规则 I1–I14 |
| `docs/SOURCES.md` | 每个断言的源文件与 locator |

## 边界

- 不把任何私钥/token 写进本仓库；凭据只记位置。
- 不在 400 人群 <GROUP_ID> 发测试消息或日志。
- 只操作 `https://chatgpt.com/dots/home`，不往其他 ChatGPT 会话发消息；代发必须标注非 Owner。
- 线上变更前先备份；一台机器同时只跑一代 Hub。
