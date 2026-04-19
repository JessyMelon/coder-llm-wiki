# Status Dashboard

## Purpose

本文件提供 `coder-llm-wiki` 的运行态总览，方便操作者和阅读者快速回答以下问题：
- 当前处于哪个阶段
- 已覆盖哪些模块和流程
- 还有哪些高风险空白
- 哪些问题需要人工确认
- 下一步最应该做什么

本文件应作为人工查看面板使用。
具体状态仍以 `progress.json` 和 `task-queue.json` 为准。

## Recommended Update Timing

建议在以下时机更新本文件：
- 完成 inventory 后
- 完成 index 后
- 每完成一批模块分析后
- 每完成一批 flow 分析后
- 每次 cross review 后
- 每次 incremental update 后

## Dashboard Template

以下结构可直接作为实际内容模板使用。

### Current Run

- Current phase:
- Current batch id:
- Last updated:
- Last snapshot:
- Diff base:

### Coverage Summary

- Inventory: `0% / 100%`
- Index: `0% / 100%`
- Modules: `0 / 0 done`
- Flows: `0 / 0 done`

### Current Priorities

1. 
2. 
3. 

### Active Blockers

- None

or

- `<blocker>`
  - Impact:
  - Needed action:

### Recently Completed

- `<task-id>` - what was completed
- `<task-id>` - what was completed

### Review Queue

- `review-needed`: 
- `blocked`: 
- `pending high-priority`: 

### High-Risk Gaps

- `<area>` - why it matters
- `<flow or module>` - why it matters

### Human Review Needed

- `<question>`
- `<conflict>`

### Suggested Next Steps

1. 
2. 
3. 

## Recommended Reading Order

当读者第一次进入 wiki 时，建议按以下顺序看：

1. `README.md`
2. `00-meta/project-charter.md`
3. `00-meta/status-dashboard.md`
4. `01-inventory/`
5. `02-index/`
6. `03-modules/` 和 `04-flows/`
7. `09-review/`

## Suggested Population Rules

### Current Run

从 `progress.json` 同步：
- `phase`
- `current_batch_id`
- `updated_at`
- `last_snapshot`
- `last_diff_base`

### Coverage Summary

从 `progress.json.coverage` 同步：
- inventory
- index
- modules_total
- modules_done
- flows_total
- flows_done

### Review Queue

从 `task-queue.json` 汇总：
- `review-needed` 数量
- `blocked` 数量
- 高优先级 `pending` 数量

### Active Blockers

优先从以下位置汇总：
- `progress.json.blockers`
- `09-review/open-questions.md`
- `09-review/human-review.md`

### High-Risk Gaps

优先标出：
- 没有模块文档的核心模块
- 没有流程文档的关键入口
- 动态路由、异步任务、外部依赖、数据一致性相关区域

## Suggested Status Meanings

### Healthy

满足以下特征：
- 当前 phase 明确
- queue 中没有失控积压
- review-needed 数量可控
- blocker 已被记录
- 最近 snapshot 存在

### At Risk

满足任一特征：
- queue 长时间未更新
- review-needed 积压过多
- 高风险模块长期未覆盖
- snapshot 长时间缺失
- blockers 存在但没有后续动作

### Stale

满足任一特征：
- 代码已经显著变更但 wiki 未更新
- evidence 引用落后于当前实现
- dashboard 与 `progress.json` 明显不一致

## Lightweight Maintenance Procedure

每次更新 dashboard 时，按以下顺序做：

1. 读取 `progress.json`
2. 读取 `task-queue.json`
3. 读取最新 snapshot
4. 检查 `09-review/` 下未解决项
5. 更新本文件的 Current Run、Coverage、Blockers、Next Steps

## Do Not

- 不要把 dashboard 写成历史流水账
- 不要复制所有 queue 细节到这里
- 不要在这里放未经证据支持的技术结论

## Bottom Line

`status-dashboard.md` 的职责不是替代状态文件，而是让人快速知道：现在系统处于什么状态，问题在哪，接下来做什么。
