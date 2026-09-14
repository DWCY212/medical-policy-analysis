# 工作流状态与返工规则

## 状态文件

在分析目录创建 `policy-analysis-state.json`：

```json
{
  "task_id": "稳定任务标识",
  "policy_title": "主政策标题",
  "policy_short_name": "最终文件名简称",
  "jurisdiction_scope": ["主政策管辖地区"],
  "time_scope": "relevance_driven",
  "mode": "manual|automatic",
  "current_step": 1,
  "documents": {
    "1": {"path": "01-政策理解.md", "status": "generated", "version": 1, "manual_confirmed": false},
    "2": {"path": "02-相关政策.xlsx", "status": "pending", "version": 0, "manual_confirmed": false},
    "3": {"path": "03-政策演进.md", "status": "pending", "version": 0, "manual_confirmed": false},
    "4": {"path": "04-初步分析.md", "status": "pending", "version": 0, "manual_confirmed": false},
    "5": {"path": "政策简称政策分析.md", "status": "pending", "version": 0, "manual_confirmed": false}
  },
  "invalidated_from": null,
  "source_checks": {
    "last_online_check_at": "YYYY-MM-DD",
    "mandatory_online_steps": [1, 2, 5],
    "needs_manual_review": []
  },
  "events": []
}
```

## 状态值

- `pending`：尚未生成或因上游变化失效。
- `generated`：产物已生成并完成本步自检。
- `manual_confirmed`：用户修改或明确确认，下游必须引用该版本。
- `stale`：保留的下游旧结果，不得作为最终结论。
- `blocked`：缺少关键输入、联网、官方核验或 Excel 工具。

## 推进条件

| 步骤 | 完成条件 |
| --- | --- |
| 1 | `01-政策理解.md` 已落盘；完成联网核验；发布日期、文号和来源状态均有值 |
| 2 | `02-相关政策.xlsx` 已落盘；四个工作表齐全；全工作表完成内容检查、公式错误扫描和渲染检查 |
| 3 | `03-政策演进.md` 已落盘；所有阶段均引用第一、二步来源 |
| 4 | `04-初步分析.md` 已落盘；九章目录完整；第一章回答四个核心问题；第四章四类证据分开；`{X}` 已替换；第七章三个子项齐全；第八章包含产品方向判断 |
| 5 | 最终 Markdown 已落盘；使用同一九章目录；关键官方页面已重新联网复核；第九章的来源字段、待确认项、转载、需人工审核和检索记录完整保留 |

第一、二、五步缺少联网证据时不得标记 `generated`。第二步只有 CSV 或伪装扩展名时不得推进。

## 依赖失效矩阵

| 用户修改 | 必须失效并重算 |
| --- | --- |
| 第 1 步 | 第 2、3、4、5 步 |
| 第 2 步 Excel | 第 3、4、5 步 |
| 第 3 步 | 第 4、5 步 |
| 第 4 步 | 第 5 步 |
| 第 5 步 | 无；仅更新最终审定稿 |

把 `invalidated_from` 设为受影响的最小步骤号，将受影响产物标为 `pending`。人工确认的上游产物不因下游重算而覆盖。

## 用户交互

- 默认自动逐步生成：每步落盘、自检并继续；只有用户明确说“手动逐步生成”才暂停。
- 手动模式每步报告产物、联网核验状态和待确认项，收到“继续”后进入下一步。
- 用户上传 Excel 修改稿时，检查工作表和必填列，登记人工确认版本后再重算下游。
- 用户只重做某一步时，将未重算下游标为 `stale` 并明确说明。
- 未选择模式不构成阻塞；如用户后来要求手动，从当前未完成步骤切换。
