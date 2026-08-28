# 医疗政策分析 Codex Plugin

面向医疗政策研究和产品市场分析的 Codex Plugin。它不预设地域或固定时间窗，而是围绕主政策的管辖地区、政策对象、治理工具和演进关系联网收集强相关证据。

## 五步输出

1. `01-政策理解.md`：联网解读主政策，核验官方来源、发布日期和文号。
2. `02-相关政策.xlsx`：联网收集强相关政策，使用四个工作表整理政策、检索记录、待确认与排除项、字段说明。
3. `03-政策演进.md`：分析已收集政策的时间线、制度关系和演进阶段。
4. `04-初步分析.md`：从产品市场经理视角分析问题发酵、政策目标、解决路径、产品定位和建设优先级。
5. `{政策简称}政策分析.md`：联网复核所有关键来源后形成最终审定稿。

支持手动逐步生成、自动逐步生成、暂停继续、单步重做，以及用户修改 Markdown 或 Excel 后按依赖关系重算下游。

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
请分析我上传的医疗政策，使用手动逐步生成。第一步联网核验发布日期和文号，第二步输出 Excel。
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
- 不限定中国大陆。中国政策使用国家、省、市及医保、卫健、药监体系；其它地区使用等效行政和监管层级。
- 不使用固定前后五年时间窗，只纳入制度关系或问题链强相关的政策。
- 找不到官方原文时标记 `待确认文件`；网址失效时标记 `需人工审核`；只有政府转载时标记 `转载`。
- 第二步必须输出并验证真正的 `.xlsx`，不能用 CSV 冒充。
- 政策原因分析区分官方事实、多来源判断、政策演进推断和待验证产品假设。

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
