---
name: medical-policy-analysis
description: >-
  对医疗政策文件进行联网核验、相关政策收集、Excel 汇总、政策演进和产品市场视角的政策成因与业务分析，并支持逐步审核、文档回传和下游重算。
  Use when the user provides a healthcare policy file or asks for official-source verification, related-policy research, policy evolution, product-market implications, or step-by-step rework across any jurisdiction.
license: MIT
allowed-tools: "Read, Write, Bash, Grep, Glob, WebSearch"
metadata:
  short-description: 医疗政策联网核验、Excel 汇总与产品市场分析
  version: "0.2.0"
  author: DWCY212
  category: SkillHub
  permissions: "workspace read/write; network read-only for research and official-source verification"
---

# 医疗政策分析

围绕用户提供的医疗政策文件执行五步可审核工作流。联网检索是核心能力；不得用模型记忆替代当前官网核验。

## Trigger / When to use

在用户提供医疗政策文件、政策链接或步骤产物，并要求完成以下任一任务时使用本 Skill：

- 解读政策并核对官方来源、发布日期、文号；
- 收集强相关政策并输出 Excel；
- 梳理政策演进阶段；
- 从产品市场经理视角分析政策出台原因和产品机会；
- 继续、修改或重做五步工作流。

不要因一般医疗知识问答、临床诊疗建议、单纯翻译或医院内部制度而触发。

## Required tools and integrations

- 使用 `Read`、`Grep`、`Glob` 检查用户文件和既有产物。
- 使用 `WebSearch` 或可用的 `$browser:control-in-app-browser`、`$chrome:control-chrome` 联网检索和打开官方页面。
- 使用 `Write` 只在任务分析目录内更新步骤产物和 `policy-analysis-state.json`。
- 第二步使用 `$spreadsheets:Spreadsheets` 创建和验证真正的 `.xlsx`；若该能力不可用，停止并报告阻塞，不得用 CSV 或改扩展名代替。
- 只用 `Bash` 执行必要的本地检查和验证；不要未经授权上传政策文件、发布内容或修改系统配置。

本 Skill 不依赖 MCP Server。用户另行提供可信 MCP 时，仍须遵守相同的来源核验和写入边界。

## Scope and input validation

1. 识别主政策、管辖地区、发布机关、政策对象和用户期望的最终政策简称；不确定时标记待确认。
2. 不预设地域限制。中国政策使用国家、省、市及医保、卫健、药监层级；其它国家或地区映射为等效的中央、区域、地方层级及医疗支付、卫生、药品监管机关，并记录管辖地区。
3. 不设置固定的前后五年时间窗。围绕主题、对象、政策工具、上下位关系、执行链条和演进关系筛选强相关政策；时间跨度服从证据，不服从固定年限。
4. 校验输入路径和文件类型。将用户原文件、人工修改稿与机器产物分开识别；人工稿登记 `manual_confirmed: true`。
5. 不提供诊断、治疗、法律结论，不纳入医院内部制度、纯商业宣传或无法说明关联的材料。

完整范围和来源规则见 [scope-and-sources.md](references/scope-and-sources.md)。

## Online verification invariants

- 每一步都允许联网补充证据；第一、二、五步必须联网。
- `发布日期` 专指该文件在官方网页、官方公报或官方索引页显示的发布日期。正文落款日期通常与其一致，不再单列；少数不一致情况写入备注。不得使用搜索结果日期、抓取日期或 PDF 元数据代替。
- 找不到任何可核验的官方信息时，将用户文件标为 `待确认文件`，官方来源网址写 `待确认文件`。
- 只有政府或主管机关转载页时标为 `转载`；网址失效或页面内容无法验证时标为 `需人工审核`。
- 不虚构 URL、日期、文号、条款或官方意图。

## Run modes

先让用户选择 `手动逐步生成` 或 `自动逐步生成`。用户未选择前不开始生成。

- 手动模式：每一步先落盘并自检，再暂停；用户回复“继续”后进入下一步。
- 自动模式：仍逐步落盘和更新状态，但完成自检后连续推进；遇到来源或工具阻塞时暂停。
- 在分析目录维护 `policy-analysis-state.json`，遵循 [workflow-and-state.md](references/workflow-and-state.md)。

## Five-step workflow

### Step 1: policy understanding and official verification

必须联网。分析用户文件，搜索原发布机关或等效官方来源，核对官方标题、发布机关、管辖地区、发布日期、文号和官方 URL。解释政策对象、措施、执行机制与待确认项，输出 `01-政策理解.md`。

### Step 2: related-policy research and Excel output

必须联网。以第一步确认的主题、对象、政策工具和执行链条为检索核心，收集强相关的同级、上位、下位、配套、替代、延续和试点政策。中国政策重点覆盖国家和省级，并补充强相关的国、省、市医保局、卫生健康委、药监局文件；其它管辖地区使用等效层级和机关。

只把关联理由清楚、证据可追溯的政策纳入核心表。创建 `02-相关政策.xlsx`，不得同时用同名 Markdown 代替。严格遵循 [excel-output.md](references/excel-output.md)，在工作簿中写入官方 URL、发布日期、文号、来源状态和关联理由，并完成全工作表渲染检查。

### Step 3: policy evolution

读取第一步和第二步工作簿，分析全部已纳入政策的时间顺序、上下位关系、政策对象、治理工具和执行机制变化。根据证据划分演进阶段，输出 `03-政策演进.md`。阶段数量和名称由材料决定，不为凑结构虚构阶段。必要时继续联网核验阶段转换事件。

### Step 4: product-market policy rationale

站在产品市场经理角度，使用前三步证据，并可联网检索官方说明、统计数据、新闻、论文和行业资料作为补充。核心逻辑是：

1. 判断政策出台前出现了什么问题、问题如何积累或发酵、政策要解决什么、通过什么机制解决。
2. 区分后发型政策与先发型政策：多数政策是问题出现并发酵后的治理响应；少数先发政策通常与大战略、人口结构、技术变革或制度前置布局有关。

以上内容仅作为内部推理框架。不要在交付文案中复述这套方法论、使用“政策潜规则”措辞，或机械展示“后发型/先发型”标签；只输出经证据支持的问题判断、解决层次、政策路径和产品启示。

输出 `04-初步分析.md`，严格使用 [document-templates.md](references/document-templates.md) 指定的目录，并用 [evidence-and-analysis.md](references/evidence-and-analysis.md) 区分事实、判断、推断和假设。

### Step 5: final review and re-verification

必须联网。审核第四步的证据链、产品判断和替代解释，重新打开或检索所有关键官方页面，复核发布日期、文号、URL 和来源状态。修正无法支撑的表述，保留待确认与需人工审核项，输出 `{政策简称}政策分析.md`。

## Continue, edit, and redo

- 用户说“继续”时，读取状态文件和上一步产物，自检通过后只执行下一步。
- 用户修改 `01-政策理解.md`、`02-相关政策.xlsx`、`03-政策演进.md` 或 `04-初步分析.md` 时，将其登记为人工确认版本并按依赖矩阵重算下游。
- 用户要求“只重做第 N 步”时只更新该步，把保留的下游标为 `stale`；不要把可能过期的内容当最终结果。
- 重算前列出将更新的文件；未经用户明确要求，不覆盖人工确认的上游产物。

## Evidence labels

- `[官方明确表述]`：政策原文、官方解读或官方答问直接表达。
- `[多来源支持的判断]`：至少两个相互独立的可靠来源支持。
- `[基于政策演进的推断]`：由时间、层级、对象或政策工具变化推导。
- `[待验证假设]`：产品或市场假设，说明需要的数据、访谈或文件。

不要把推测写成政府官方意图。每个关键判断紧跟来源编号或 URL。

## Error handling

1. 官方页面无法访问：尝试同一机关的公报、索引页或正式附件；仍无法验证时标记 `需人工审核`。
2. 找不到官方来源：标记 `待确认文件`，不要用数据库或媒体页面冒充官方 URL。
3. 标题、日期或文号冲突：并列记录证据，优先正式文件正文和原发布机关页面，保留冲突说明。
4. Excel 创建或渲染失败：保留已核验数据，修复后重新导出；工具不可用时将第二步设为 `blocked`。
5. 状态文件损坏：备份现有文件，从产物推断状态；无法确认依赖时不覆盖下游。

## Examples

```text
请分析我上传的连续用药政策，手动逐步生成。第一步联网核对发布日期和文号，第二步输出 Excel。
```

```text
我修改了 02-相关政策.xlsx，请把它作为人工确认版本，从第三步继续。
```

## References / See Also

- [scope-and-sources.md](references/scope-and-sources.md)：动态地域、相关性和官方来源规则。
- [workflow-and-state.md](references/workflow-and-state.md)：状态、暂停继续与返工矩阵。
- [document-templates.md](references/document-templates.md)：第一、三、四、五步文档结构。
- [excel-output.md](references/excel-output.md)：第二步 Excel 工作簿结构和验证规则。
- [evidence-and-analysis.md](references/evidence-and-analysis.md)：产品市场经理视角的政策成因分析内部方法。
