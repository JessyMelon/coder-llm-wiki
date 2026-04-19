# Snapshot Format

## Purpose

本文件定义 `coder-llm-wiki/10-snapshots/` 下快照文件的统一格式。

目标：
- 让中断恢复不依赖长上下文回忆
- 让不同操作者都能快速接手当前批次
- 让 snapshot 成为真实工作锚点，而不是随手备注

## Naming Convention

推荐命名：

`YYYY-MM-DD-HHMM-<batch-id>.md`

示例：

`2026-04-19-1530-bootstrap.md`

## Required Sections

每个 snapshot 文件都应包含以下章节。

### 1. Snapshot Meta

- Timestamp:
- Batch ID:
- Current Phase:
- Operator:
- Trigger:
  - batch complete
  - pause
  - blocked
  - incremental update complete

### 2. Current State

- `progress.json` summary
- `task-queue.json` summary
- current coverage summary

### 3. Completed In This Batch

- `<task-id>` - `<what changed>`
- `<task-id>` - `<what changed>`

### 4. Outputs Written

- `path/to/file`
- `path/to/file`

### 5. Review Status

- Passed:
- Pass With Questions:
- Failed:
- Review-needed:

### 6. Active Blockers

- `<blocker>`
  - impact:
  - required action:

### 7. Open Questions

- `<question>`
- `<question>`

### 8. Next Recommended Steps

1. 
2. 
3. 

### 9. Resume Instructions

- Read these files first:
  - `00-meta/progress.json`
  - `00-meta/task-queue.json`
  - `00-meta/status-dashboard.md`
  - this snapshot
- Resume from:
  - `<task-id or phase>`

## Minimal Snapshot Template

```md
# Snapshot: <batch-id>

## Snapshot Meta
- Timestamp:
- Batch ID:
- Current Phase:
- Operator:
- Trigger:

## Current State
- Progress:
- Queue:
- Coverage:

## Completed In This Batch
-

## Outputs Written
-

## Review Status
- Passed:
- Pass With Questions:
- Failed:
- Review-needed:

## Active Blockers
-

## Open Questions
-

## Next Recommended Steps
1. 
2. 
3. 

## Resume Instructions
- Read these files first:
  - `00-meta/progress.json`
  - `00-meta/task-queue.json`
  - `00-meta/status-dashboard.md`
  - this snapshot
- Resume from:
```

## Writing Rules

1. Snapshot 必须写“下一步做什么”，不能只写“做了什么”。
2. Snapshot 必须写 blocker 和 unresolved questions。
3. Snapshot 应引用实际产物路径，而不是抽象描述。
4. 如果 queue 与真实产物不一致，必须明确指出。
5. 如果本批次没有新产物，也要说明原因。

## When To Write A Snapshot

必须写 snapshot 的时机：

1. 完成一批 module tasks 后
2. 完成一批 flow tasks 后
3. 完成 cross review 后
4. 准备暂停工作时
5. 完成一次较大的 incremental update 后
6. 遇到 blocker 且短期无法继续时

## Anti-Patterns

不要这样写 snapshot：

- 只写一句“done”
- 不写 next steps
- 不写 blocker
- 不写实际产物
- 把 snapshot 写成和 dashboard 完全重复的泛化摘要

## Bottom Line

snapshot 的价值在于“让下一个人能继续”，不是“证明上一个人工作过”。
