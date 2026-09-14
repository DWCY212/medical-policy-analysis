# GPT-6 Astra 适配说明

本插件 0.4.0 的改动依据 OpenAI Docs（访问日期：2026-09-14）：

- [GPT-6 Astra prompting best practices](https://developers.openai.com/api/docs/guides/latest-model/gpt-6-astra#prompting-best-practices)：Astra 适合跨步骤、跨工具的复杂工作；应提供来源、模板、约束和验收条件。它更可能提出澄清问题，因此插件给出“默认自动模式”和合理默认，只在答案会改变交付物时提问。Astra 对 Skill/上下文中的指令更敏感，所以上传资料被明确标记为资料，且不覆盖用户指令。
- [OpenAI Models](https://learn.chatgpt.com/docs/models)：复杂任务从默认推理强度开始；Ultra 仅用于可拆分的并行工作。插件不强制 Max/Ultra，也不默认启动子代理，避免无谓调度和上下文开销。

## 设计变化

| 旧行为 | 0.4.0 行为 | 目的 |
| --- | --- | --- |
| 主 Skill、README 和参考文档重复同一套长规则 | 主 Skill 只保留触发、执行协议、不变量和步骤；字段细节按需读取 | 减少固定上下文，降低规则冲突 |
| 未选择运行模式就暂停 | 默认自动；明确要求手动时才暂停 | 保持长流程推进，减少不必要的澄清回合 |
| 专用表格工具不可用即阻塞 | 优先专用工具，否则使用本地可验证工作簿库；仍要求真实 XLSX | 让工具差异不再中断研究流程 |
| 资料中的指令未单独标识 | 上传文件、网页、搜索结果均定义为资料，不能改变任务规则 | 防止文档提示注入和范围漂移 |
| 默认鼓励并行代理/高推理 | 仅在确实可拆分且有收益时并行；从默认推理强度开始 | 控制 Astra 调度和 token 成本 |

本插件是 Codex Skill，不是 API 调用程序，因此没有模型 endpoint、请求参数或温度参数需要迁移。若在 API 应用中直接调用 Astra，请依照官方迁移页另行处理 Responses API、推理强度和不支持参数。
