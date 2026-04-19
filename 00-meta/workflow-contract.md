# Workflow Contract

## Purpose

本文件定义 `coder-llm-wiki` 的标准工作流合同，用于统一分析任务的输入、输出、进入条件、退出条件、失败处理和下一步动作。

目标：
- 让不同操作者按同一套流程工作
- 让每个阶段的产物可以被下一阶段直接消费
- 让中断恢复和增量更新有稳定依据

## Global Rules

所有阶段都必须遵守以下规则：

1. 重要结论必须有 evidence 支撑。
2. 事实、推断、问题必须分开写。
3. 无法确认的内容进入 `Open Questions`，不得伪装成事实。
4. 每次任务结束后都要更新状态文件。
5. 每次批量任务结束后都要写 snapshot。
6. 如阶段输出未达标，不得直接推进到下一阶段。

## Phase 0: Initialize

### Goal

初始化本轮 wiki 分析工作上下文。

### Inputs

- 仓库根目录
- `coder-llm-wiki/00-meta/project-charter.md`
- 当前 `progress.json`
- 当前 `task-queue.json`

### Required Reads

- `coder-llm-wiki/README.md`
- `coder-llm-wiki/00-meta/project-charter.md`
- `coder-llm-wiki/00-meta/opencode-dispatch-checklist.md`

### Outputs

- 更新后的 `coder-llm-wiki/00-meta/progress.json`
- 更新后的 `coder-llm-wiki/00-meta/task-queue.json`
- 如需要，更新 `coder-llm-wiki/00-meta/glossary.md`

### Entry Criteria

- `coder-llm-wiki/` 已存在

### Exit Criteria

- `progress.json.phase` 已设置为当前阶段
- 首批任务已写入 queue
- 命名和范围约束已知

### Failure Handling

- 如果 meta 文件缺失，先记录 blocker，再补齐缺失文件
- 如果已有历史状态冲突，写入 `09-review/human-review.md`

### Next Step

- 若仓库认知缺失，进入 `Inventory`
- 若 inventory 已完成，进入 `Index`

## Phase 1: Inventory

### Goal

建立仓库顶层认知地图和主要入口视图。

### Inputs

- 根目录结构
- README、构建配置、部署配置、顶层脚本、入口文件

### Outputs

- `01-inventory/repo-map.md`
- `01-inventory/tech-stack.md`
- `01-inventory/entrypoints.md`
- `01-inventory/module-candidates.json`

### Entry Criteria

- 尚未具备可靠的顶层仓库地图

### Exit Criteria

- 顶层目录职责已标注
- 主要运行入口已识别
- 模块候选集合可供切分任务

### Failure Handling

- 如果入口无法判定，记录到 `09-review/open-questions.md`
- 如果仓库规模过大，允许按子系统拆 inventory 批次

### Next Step

- 进入 `Index`

## Phase 2: Index

### Goal

建立后续分析复用的检索层，避免每次重新全局扫描。

### Inputs

- Inventory 产物
- 真实入口、核心符号、测试目录

### Outputs

- `02-index/routes.md`
- `02-index/symbols.md`
- `02-index/test-map.md`
- 如有必要，补充 `jobs.md` 或 `events.md`

### Entry Criteria

- Inventory 已完成，且可识别真实入口

### Exit Criteria

- 至少能从入口追到核心模块或测试
- 后续模块分析不再依赖重复全仓扫描

### Failure Handling

- 如果路由或任务系统过于动态，明确标注“索引不完整的原因”
- 如果测试映射不足，标记为后续 review 风险

### Next Step

- 进入 `Prepare Module Queue`

## Phase 3: Prepare Module Queue

### Goal

将模块候选切分为稳定、可执行的小任务。

### Inputs

- `01-inventory/module-candidates.json`
- `02-index/` 下索引文档

### Outputs

- 更新后的 `00-meta/task-queue.json`

### Entry Criteria

- 已有模块候选列表

### Exit Criteria

- 每个模块任务都有 scope、outputs、status
- 当前批次任务数量可控，便于 review

### Failure Handling

- 如果模块边界不稳定，先记录为候选分组，不强行定稿
- 如果任务过大，继续拆分而不是保留巨型模块任务

### Next Step

- 进入 `Module Analysis`

## Phase 4: Module Analysis

### Goal

沉淀单模块的职责、边界、依赖、数据交互和风险。

### Inputs

- 模块任务 scope
- 相关入口、配置、测试、上游调用方

### Outputs

- `03-modules/<module>.md`
- `08-evidence/<module>.refs.md`
- `09-review/<module>.questions.md`

### Entry Criteria

- 模块任务状态为 `pending`

### Exit Criteria

- 模块文档字段完整
- evidence 已链接
- unresolved gaps 已写入 review 文件
- 任务状态更新为 `review-needed`

### Failure Handling

- 如果模块无法独立切分，记录边界问题并返回 queue 重拆
- 如果关键源码不可读或生成内容不可信，标记为 `blocked`

### Next Step

- 进入 `Lightweight Review`

## Phase 5: Lightweight Review

### Goal

对单模块产物执行最小审查，避免低质量内容进入已完成状态。

### Inputs

- 模块文档
- evidence 文档
- question 文档

### Outputs

- 更新后的 queue 状态
- 必要时修订模块文档或 evidence 文档

### Entry Criteria

- 任务状态为 `review-needed`

### Exit Criteria

- 通过则标记为 `done`
- 不通过则回退到 `pending` 或标记为 `blocked`

### Failure Handling

- 如果 evidence 不足，退回补证据
- 如果结论与源码冲突，记录到 `09-review/conflict-log.md`

### Next Step

- 当前批次未完成则回到 `Module Analysis`
- 批次完成则进入 `Flow Planning`

## Phase 6: Flow Planning

### Goal

确定最值得文档化的关键流程。

### Inputs

- 入口清单
- 模块文档
- 测试映射
- 风险区域

### Outputs

- 更新后的 `00-meta/task-queue.json`

### Entry Criteria

- 已具备一定模块知识和入口索引

### Exit Criteria

- flow 任务已按业务价值或风险排序
- 每个 flow 任务有明确入口和预期输出

### Failure Handling

- 如果流程边界不清晰，允许先建 exploratory flow 任务

### Next Step

- 进入 `Flow Analysis`

## Phase 7: Flow Analysis

### Goal

从真实入口出发，追踪主路径、失败路径和副作用。

### Inputs

- flow 任务定义
- 入口文件
- 相关模块文档
- 相关测试

### Outputs

- `04-flows/<flow>.md`
- 必要时补充 `08-evidence/*.md`

### Entry Criteria

- flow 任务状态为 `pending`

### Exit Criteria

- 主路径是顺序叙述，不是纯函数列表
- 失败路径、状态变化、外部调用已明确
- 任务状态更新为 `review-needed`

### Failure Handling

- 如果流程包含大量动态分派，记录证据限制和推断边界
- 如果缺关键调用链，标记为 `blocked`

### Next Step

- 进入 `Cross Review`

## Phase 8: Cross Review

### Goal

跨模块、流程、索引文档做一致性和证据校验。

### Inputs

- `03-modules/`
- `04-flows/`
- `08-evidence/`
- `02-index/`

### Outputs

- `09-review/conflict-log.md`
- `09-review/open-questions.md`
- `09-review/human-review.md`

### Entry Criteria

- 已有一批模块或流程文档完成初稿

### Exit Criteria

- 关键冲突已修复或显式挂起
- 高风险缺口已被记录
- review 结论可指导下一轮补写或修订

### Failure Handling

- 如果冲突无法仅靠代码解出，转入 human-review
- 如果多文档缺 evidence，整体回退到补证据批次

### Next Step

- 进入 `Snapshot`
 - 或继续下一轮 `Module Analysis` / `Flow Analysis`

## Phase 9: Snapshot

### Goal

记录当前批次的可恢复状态。

### Inputs

- 最新 `progress.json`
- 最新 `task-queue.json`
- 当前批次产物和 blocker

### Outputs

- `10-snapshots/<timestamp>-<batch>.md`

### Entry Criteria

- 当前批次完成、暂停或遇到明显阻塞

### Exit Criteria

- 已记录完成项、未完成项、阻塞项、下一步
- `progress.json.last_snapshot` 已更新

### Failure Handling

- 如果状态不完整，至少写最小快照，不允许无记录中断

### Next Step

- 暂停
- 或继续下一批任务

## Phase 10: Incremental Update

### Goal

在代码变化后，只更新受影响的 wiki 内容。

### Inputs

- 当前 `git diff` 或 changed files
- 现有模块、流程、evidence 文档

### Outputs

- 被影响文档的局部更新
- 新增 review 项
- 必要时新增 snapshot

### Entry Criteria

- 已存在基础 wiki 文档
- 有明确代码变化范围

### Exit Criteria

- 受影响的模块和流程已重审
- 陈旧 evidence 已修正
- 新不确定项已被登记

### Failure Handling

- 如果无法建立 diff 到文档的映射，先进入 inventory/index 补索引
- 如果变化过大，允许退化为局部重建批次

### Next Step

- 返回 `Cross Review`
- 或进入 `Snapshot`

## Allowed Phase Transitions

推荐状态流转：

1. `Initialize -> Inventory -> Index -> Prepare Module Queue -> Module Analysis -> Lightweight Review`
2. `Lightweight Review -> Module Analysis`，直到模块批次完成
3. `Lightweight Review -> Flow Planning -> Flow Analysis -> Cross Review`
4. `Cross Review -> Snapshot`
5. 代码变更后：`Incremental Update -> Cross Review -> Snapshot`

## Definition Of Done

一次 wiki 分析批次只有在满足以下条件时才算完成：

1. 相关文档已经写入目标目录。
2. 关键结论已经附证据。
3. 不确定项已转入 review 文档。
4. 状态文件已更新。
5. 已写入 snapshot 或明确标注无需新快照。

## Operational Notes

- 优先让 workflow 稳定，而不是追求一次覆盖所有仓库内容。
- 优先完成高价值模块和关键流程，而不是平均覆盖所有目录。
- review 文档不是失败产物，而是工作流的一部分。
- 如果需要自动化，先自动化状态更新和 gate 检查，再自动化全文生成。
