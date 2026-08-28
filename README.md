# 医疗政策分析 Codex Plugin

这是一个面向中国大陆医疗政策研究的 Codex Plugin，重点覆盖医保、卫生健康和药品监管政策，支持国家、省、市三级政策资料整理。

## 能力

插件按五步生成可审核的 Markdown 文档：

1. `01-政策理解.md`：政策身份、官方来源网址、发布日期、文号和内容解读。
2. `02-相关政策.md`：国家、省、市相关政策检索及来源表格。
3. `03-政策演进.md`：政策时间线和演进阶段。
4. `04-初步分析.md`：政策出台问题背景、原因链和潜规则分析。
5. `xx政策分析.md`：审核调整后的最终政策分析。

每个任务还会维护 `policy-analysis-state.json`，支持手动逐步生成、自动逐步生成、断点恢复、单步重做，以及用户修改后按依赖关系重算下游步骤。

## 从 GitHub 加载

将本仓库发布到 GitHub 后，在 Codex 中执行：

```bash
codex plugin marketplace add OWNER/REPOSITORY
codex plugin add medical-policy-analysis@medical-policy-analysis
```

把 `OWNER/REPOSITORY` 替换为实际的 GitHub 仓库，例如 `example-org/medical-policy-analysis`。首次添加 marketplace 后，Codex 会读取仓库根目录的 `.agents/plugins/marketplace.json`。

## 从本地目录加载

```bash
codex plugin marketplace add "C:/path/to/medical-policy-analysis"
codex plugin add medical-policy-analysis@medical-policy-analysis
```

本地路径应指向包含 `.agents/plugins/marketplace.json` 的仓库根目录，而不是直接指向 `plugins/medical-policy-analysis`。

## 更新插件

代码推送到 GitHub 后刷新 marketplace，再重新安装插件：

```bash
codex plugin marketplace upgrade medical-policy-analysis
codex plugin add medical-policy-analysis@medical-policy-analysis
```

建议更新后新建一个 Codex task，使新的 Skill 内容被加载。

## 目录结构

```text
.
├── .agents/plugins/marketplace.json
├── plugins/medical-policy-analysis/
│   ├── .codex-plugin/plugin.json
│   ├── skills/medical-policy-analysis/SKILL.md
│   └── references/
└── README.md
```

插件不内置 MCP 服务或外部 API。政策检索、官方来源核验和补充资料分析由 Codex 在执行 Skill 时完成；找不到原文、网址失效或只有转载页时，输出会保留相应人工审核标记。
