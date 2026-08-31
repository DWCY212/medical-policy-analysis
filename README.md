# 医疗政策分析 Codex Plugin

面向医疗政策研究和产品市场分析的 Codex Plugin。它不预设国家、地区或固定时间窗，而是围绕主政策的管辖地区、政策对象、治理工具和演进关系联网收集强相关证据。

## 五步输出

1. `01-政策理解.md`：联网解读主政策，核验官方来源、发布日期和文号。
2. `02-相关政策.xlsx`：联网收集强相关政策，使用四个工作表整理政策、检索记录、待确认与排除项、字段说明。
3. `03-政策演进.md`：分析已收集政策的时间线、制度关系和演进阶段。
4. `04-初步分析.md`：从产品市场经理视角整合前面材料，分析政策出台原因、问题、目标、解决路径、产品定位、建设优先级、验证计划与合规边界。
5. `{政策简称}政策分析.md`：联网复核所有关键来源后，按最终九章目录形成审定稿。

上传资料后、开始分析前会先要求选择运行模式：

```text
请选择本次分析的运行模式：
自动逐步生成：连续完成五步。
手动逐步生成：每完成一步暂停，可对当前步进行调整，等你回复“继续”后再进入下一步。
```

支持暂停继续、单步重做，以及用户修改 Markdown 或 Excel 后按依赖关系重算下游。

## 第四步与最终报告目录

第四步初步分析和第五步最终审定稿使用相同目录：

1. 审定信息（不编号）
2. 一、政策研究后的核心结论
3. 二、政策原文能够直接确认的内容
   - 官方来源复核表
   - 相关政策核心摘要
4. 三、政策演进与阶段判断
5. 四、政策出台背后问题的推理
6. 五、政策要解决的 `X` 个层次
7. 六、政策采用的解决路径
8. 七、对产品设计的业务启示
   - 产品定位
   - 建议的建设优先级
   - 验证计划与合规边界
9. 八、结论与方向判断
10. 九、资料来源汇总
   - 待确认文件与需人工审核项
   - 检索记录

## 安装

需要可使用 `codex` 命令并支持 Codex Plugin 的环境：

```bash
codex plugin marketplace add DWCY212/medical-policy-analysis --ref main
codex plugin add medical-policy-analysis@medical-policy-analysis
```

检查状态：

```bash
codex plugin list
```

看到以下状态表示安装成功：

```text
medical-policy-analysis@medical-policy-analysis  installed, enabled
```

安装后新建一个 Codex task，例如：

```text
我已上传医疗政策资料，请使用手动逐步生成。第一步联网核验发布日期和文号，第二步输出 Excel。
```

## 更新

```bash
codex plugin marketplace upgrade medical-policy-analysis
codex plugin add medical-policy-analysis@medical-policy-analysis
```

更新后新建一个 Codex task，使新 Skill 生效。

## 卸载

```bash
codex plugin remove medical-policy-analysis@medical-policy-analysis
codex plugin marketplace remove medical-policy-analysis
```

## 本地开发者安装

在仓库根目录执行：

```bash
codex plugin marketplace add .
codex plugin add medical-policy-analysis@medical-policy-analysis
```

## 核心规则

- 每一步都可以联网；第一、二、五步必须联网核验。
- `发布日期` 是官方网页、公报或索引页显示的文件发布日期；正文落款不一致时在备注中说明。
- 分析范围不限于中国。中国政策使用国家、省、市及医保、卫健、药监体系；其它地区使用等效行政和监管层级。
- 不使用固定前后五年时间窗，只纳入制度关系或问题链强相关的政策。
- 找不到官方原文时标记 `待确认文件`；网址失效时标记 `需人工审核`；只有政府转载时标记 `转载`。
- 第二步必须输出并验证真正的 `.xlsx`，不能用 CSV 冒充。
- 第一章整合所有内容，必须回答为什么出台、出现什么问题、要解决什么问题、怎么去解决。
- 第三章必须呈现政策演进与阶段判断。
- 第四章区分官方明确表述、多来源支持的判断、基于政策演进的推断和待验证假设。
- 第五章的 `X` 必须替换为实际层次数量，任何交付文档不得保留占位符。
- 第七章必须包含产品定位、建议的建设优先级、验证计划与合规边界。
- 第八章综合全部内容，给出结论和产品设计方向判断，并说明成立条件、风险和不宜外推的边界。
- 第九章列出来源网址、发布日期、文号、来源状态、待确认文件、转载、需人工审核和检索记录。
- “政策潜规则”及其内部分类只作为分析过程中的内部规则，不得出现在第四步或最终报告目录和正文中。

## 仓库结构

```text
.
|-- .agents/plugins/marketplace.json
|-- plugins/medical-policy-analysis/
|   |-- .codex-plugin/plugin.json
|   `-- skills/medical-policy-analysis/
|       |-- SKILL.md
|       `-- references/
|-- .gitignore
|-- LICENSE
`-- README.md
```

## 许可证与责任边界

本项目采用 MIT License。政策文件版权和使用限制以原发布机关规定为准。本插件不替代医疗、法律、合规或政府政策专业意见。

## 问题反馈

请在 GitHub Issues 中提供 Codex 版本、操作系统、执行命令、完整错误信息和插件版本。不要提交访问令牌、未公开政策材料或个人敏感信息。
