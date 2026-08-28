# 医疗政策分析 Codex Plugin

面向中国大陆医疗政策研究的 Codex Plugin，重点分析医保、卫生健康和药品监管政策，覆盖国家、省、市三级资料。

插件会按五个步骤生成 Markdown 文档：

1. `01-政策理解.md`：政策内容、官方来源网址、发布日期和文号。
2. `02-相关政策.md`：相关国家级、省级和市级政策及来源表格。
3. `03-政策演进.md`：政策时间线和演进阶段。
4. `04-初步分析.md`：问题背景、政策原因链和政策潜规则分析。
5. `xx政策分析.md`：核验调整后的最终政策分析。

支持手动逐步生成、自动逐步生成、暂停后继续、单步重做，以及用户修改某一步文档后自动重算受影响的后续步骤。

## 一键安装方式

需要安装支持 Codex Plugin 的 Codex，并确保可以在终端使用 `codex` 命令。

在 Codex 终端中执行：

```bash
codex plugin marketplace add DWCY212/medical-policy-analysis --ref main
codex plugin add medical-policy-analysis@medical-policy-analysis
```

安装完成后检查状态：

```bash
codex plugin list
```

看到以下内容即表示插件已启用：

```text
medical-policy-analysis@medical-policy-analysis  installed, enabled
```

安装后建议新建一个 Codex task，再发送类似下面的请求：

```text
请分析我提供的医疗政策文件。先让我选择手动逐步生成或自动逐步生成。
```

## 更新插件

仓库发布新版本后，在 Codex 中执行：

```bash
codex plugin marketplace upgrade medical-policy-analysis
codex plugin add medical-policy-analysis@medical-policy-analysis
```

更新后新建一个 Codex task，使新的 Skill 内容生效。

## 卸载插件

只卸载插件：

```bash
codex plugin remove medical-policy-analysis@medical-policy-analysis
```

同时移除 GitHub marketplace 配置：

```bash
codex plugin marketplace remove medical-policy-analysis
```

## 本地开发者安装

克隆仓库后，从仓库根目录执行：

```bash
codex plugin marketplace add .
codex plugin add medical-policy-analysis@medical-policy-analysis
```

仓库根目录必须包含 `.agents/plugins/marketplace.json`。不要把 `plugins/medical-policy-analysis` 目录直接作为 marketplace 根目录。

## 输出规则

- 只分析中国大陆政策；国家和省级为重点，市级为补充。
- 医保局、卫生健康委和药监局为核心部门。
- 主政策前五年至当前日期，以及主政策发布后至当前日期，为默认检索范围。
- 政府网站是最终官方来源；政策数据库只作为检索线索。
- 找不到原文时标记为“待确认文件”；网址失效时标记为“需人工审核”。
- 政府转载页标记为“转载”，并继续寻找原始发布页。
- 政策原因分析区分官方明确表述、多来源支持的判断、基于政策演进的推断和待验证假设。

## 仓库结构

```text
.
|-- .agents/plugins/marketplace.json
|-- plugins/medical-policy-analysis/
|   |-- .codex-plugin/plugin.json
|   |-- skills/medical-policy-analysis/SKILL.md
|   `-- references/
|-- .gitignore
|-- LICENSE
`-- README.md
```

## 许可证

本项目采用 MIT License。政策文件的版权和使用限制以原发布机关规定为准；本插件不替代法律、合规或专业政策意见。

## 问题反馈

请在 GitHub 仓库的 Issues 中提供：Codex 版本、操作系统、执行的命令、完整错误信息，以及相关插件版本。不要在 Issue 中提交未公开的政策材料或访问令牌。
