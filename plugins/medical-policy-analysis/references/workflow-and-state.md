# 工作流状态与返工规则

## 状态文件

在分析目录创建 `policy-analysis-state.json`。推荐结构如下，字段可扩展但不要删除核心字段：

```json
{
  "task_id": "可读且稳定的任务标识",
  "policy_title": "主政策标题",
  "policy_short_name": "用于最终文件名的简称",
  "mode": "manual|automatic",
  "current_step": 1,
  "documents": {
    "1": {"path": "01-政策理解.md", "status": "generated", "version": 1, "manual_confirmed": false},
    "2": {"path": "02-相关政策.md", "status": "pending", "version": 0, "manual_confirmed": false},
    "3": {"path": "03-政策演进.md", "status": "pending", "version": 0, "manual_confirmed": false},
    "4": {"path": "04-初步分析.md", "status": "pending", "version": 0, "manual_confirmed": false},
    "5": {"path": "xx政策分析.md", "status": "pending", "version": 0, "manual_confirmed": false}
  },
  "invalidated_from": null,
  "source_checks": {"last_checked_at": "YYYY-MM-DD", "needs_manual_review": []},
  "events": []
}
```

实际 JSON 键名可使用 ASCII 版本（例如 `manual_confirmed`），上面的中文键仅用于说明；推荐实现时统一使用 `manual_confirmed`，避免程序解析歧义。

## 状态值

- `pending`：尚未生成或因上游变化失效。
- `generated`：机器生成且已完成本步自检。
- `manual_confirmed`：用户修改或明确确认过的内容；下游必须引用该版本。
- `stale`：用户要求只重做前一步而暂时保留的旧下游结果，禁止当作最终结论。
- `blocked`：缺少关键输入或来源核验无法完成，需要用户补充。

## 依赖失效矩阵

| 用户修改 | 必须失效并重算 |
| --- | --- |
| 第 1 步 | 第 2、3、4、5 步 |
| 第 2 步 | 第 3、4、5 步 |
| 第 3 步 | 第 4、5 步 |
| 第 4 步 | 第 5 步 |
| 第 5 步 | 无；仅更新最终审定稿 |

把 `invalidated_from` 设为受影响的最小步骤号，并把受影响文档标为 `pending`。人工确认的上游文档不因下游重算而覆盖；在下游元信息中写明引用的版本号。

## 用户交互协议

- 首次运行：说明模式、分析目录和预计五份文件，生成第 1 步后按模式继续或暂停。
- 手动模式暂停语句应明确：“第 N 步已生成：路径。请回复‘继续’进入第 N+1 步，或上传修改后的该步骤文档。”
- 上传文档时先回显识别到的步骤和将重算的范围，再执行；识别不确定时停下请求用户指定步骤。
- “只重做第 N 步”不自动删除或重算下游，但将下游标为 `stale` 并在最终交付中警告。
- 自动模式也要在每份文件写入后更新状态和事件，以便中途中断后从最近完成步骤恢复。
