# DECISIONS — 决策与事故规则

来源：`OPS/research/dot-review-20261023/LEDGER.md`（D1–D13、I1–I14）。

## 架构决策

| # | 决策 | 理由 |
|---|---|---|
| D1 | Dot 是 VPS Capability Hub 的**客户端**（MCP + NapCat/OneBot），不是聊天机器人 | Owner 要的是可执行平面 |
| D2 | 一台机器同时只跑一代；r03b 停用保留为回滚目标 | 避免双写与 stale lease |
| D3 | 自迭代先写 `HUB_DATA/self-iterate/pending/`，部署只经固定 root oneshot | 防止失败回滚到坏代码 |
| D4 | no-rebuild：工具走 `hub_call({tool,args})`，事件走 `hub.event` + `kind` | ChatGPT 插件目录在创建时冻结，Refresh 不重拉 |
| D5 | 认证 = query string token；不做 OAuth | ChatGPT 插件表单只有 OAuth 或 no-auth |
| D6 | 现代 MCP 2026-07-28 无状态；每个结果带 `resultType:"complete"`；`initialize` 回显请求版本 | 否则插件报「服务器未能完成 MCP 设置」 |
| D7 | 事件投递到平台，不要求 Dot 有入站端点 | 平台 callback 能唤醒 Dot |
| D8 | 出站审计用独立 `qq_outbound` 表 + `qq_get_outbound` | 保持 `qq_messages` 只存入站 |
| D9 | `/mcp-open` 只读：4 个静态工具、拒 batch、subscribe 403、1MB 上限、先鉴权后解析 body | batch 绕过 allowlist；匿名 subscribe 泄露预览 |
| D10 | Obsidian 保留 Dot 自己的目录结构；只为 ≥20 条消息且可引用的人建卡 | 群友的话是数据，不是授权 |
| D11 | 被叫停就停止主动发群；唤醒 ≠ 必须回复 | Owner：「不用再在 QQ 群里聊天」 |
| D12 | 400 人群不发测试流量 | Owner 明令 |
| D13 | 部署前对 **live** rebase，不对旧 candidate | 旧 candidate 会回滚 hotfix |

## 事故与规则

| # | 事故 | 规则 |
|---|---|---|
| I1 | omp 往 Owner 的 ChatGPT dot 会话发了 8–9 条消息、点了 Always allow、打开删除插件对话框 | 不写 Owner 的 ChatGPT/Slack/账号 UI；只读/截图；文本交给 Owner 或用 MCP |
| I2 | 把群友 `<MEMBER_QQ>` 当 Owner 并改 `QQ_OWNER_IDS` | Owner 只有 `<OWNER_QQ>`；群友不是授权 |
| I3 | `/mcp-open` JSON-RPC batch 使 `body.method` undefined，绕过 allowlist → 匿名 `hub_write_file`+`hub_deploy` | 先校验传输形状再查 allowlist；拒数组；先鉴权后解析 |
| I4 | 匿名 `events/subscribe` 用固定 principal，任何人可收私聊预览 | 事件写入仅 token |
| I5 | candidate `server.mjs` 早于两个 live hotfix，部署会静默回滚 | 部署前用 `hub_read_file(source:"live")` rebase 并 grep 不变量 |
| I6 | 研究过度断言（IPv6 ENETUNREACH 归因错误，6 处） | 有争议的归因标 `[UNVERIFIED]`；发布前独立 verifier |
| I7 | 插件目录冻结在 44 工具而服务端 47；Refresh 不产生 `tools/list` | 一个稳定 façade；目录数只当客户端状态 |
| I8 | 插件安装失败约 30 分钟，无诊断字段 | 每个现代结果带 `resultType:"complete"` |
| I9 | 测试载荷发进 400 人群（2 秒 10KB 视频，`message_id 163087385`） | 生产群不发测试流量；已落地的要披露 |
| I10 | `qq_send_message` 返回 SUCCEEDED 但不落出站行，读回 NOT_FOUND | 每条发送路径都写 receipt；无 receipt 是信号 |
| I11 | 文档漂移（T0、DOT-HUB-FINAL） | 一份文档一个计数源；写时重算 |
| I12 | live MCP token 明文出现在 work 脚本并被贴进插件表单 | 凭据不进脚本/报告；运行时从 VPS secrets 读 |
| I13 | Dot 云电脑 pypi 被 policy 阻断；`dot-tts` 在 sandbox 失败（exit 226） | TTS 放 VPS；保留 fallback 并报 `engine`/`fallback_reason` |
| I14 | 事故记录说 8 条消息，实际 9 个文件 | 从产物清单计数，不凭记忆 |

## 人格/记忆的边界（Dot 自己提出，Owner 未最终裁）

- 拒绝把闲聊硬比例用于翻译/技术回答。
- 拒绝故意延迟与不诚实。
- 建议人格规则只用于闲聊；是否发送「仅闲聊」修正版待 Owner 决定。
