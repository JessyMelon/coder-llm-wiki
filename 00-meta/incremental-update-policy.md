# Incremental Update Policy

## Purpose

本文件定义当仓库代码发生变化时，`coder-llm-wiki` 应如何进行增量维护。

目标：
- 只更新受影响的文档
- 避免整库重写
- 让 evidence 与当前代码保持一致
- 把无法确认的变化明确登记到 review 文档

## Core Principle

增量更新的默认策略是：

1. 先识别变化范围
2. 再映射到受影响的模块和流程
3. 只修改必要文档
4. 最后执行 review 和 snapshot

如果不能可靠映射影响范围，不要盲目大改，应先补索引或局部重建。

## Standard Update Flow

### Step 1: Read Change Scope

优先读取：
- `git diff`
- changed files 列表
- 最近合并或提交涉及的目录

记录：
- 哪些源码文件变了
- 哪些配置文件变了
- 哪些测试文件变了
- 是否有入口变更

### Step 2: Classify Change Type

将变更归类为以下一种或多种：

- `module-internal`
  - 模块内部实现变化，边界不一定变化
- `interface-change`
  - 导出接口、类型、参数、返回值变化
- `entrypoint-change`
  - HTTP、CLI、worker、job、consumer 等入口变化
- `flow-change`
  - 主路径、失败路径、状态变化或外部调用变化
- `config-change`
  - 配置键、环境变量、feature flag、部署配置变化
- `test-change`
  - 测试覆盖关系变化
- `rename-or-move`
  - 文件或目录重命名、迁移
- `deletion`
  - 功能、入口、测试或模块被删除

### Step 3: Map Changes To Wiki Artifacts

根据变化类型映射受影响文档。

#### Source File Change

优先检查：
- 对应 `03-modules/<module>.md`
- 对应 `08-evidence/<module>.refs.md`
- 引用该模块的 flow 文档

#### Entrypoint Change

必须检查：
- `01-inventory/entrypoints.md`
- `02-index/routes.md` 或其他入口索引
- 对应 `04-flows/<flow>.md`
- 相关 evidence 文档

#### Config Change

必须检查：
- `01-inventory/tech-stack.md`
- 相关模块文档中的 `Configuration Dependencies`
- `06-ops/` 下相关运维文档
- 相关 risk 文档

#### Test Change

必须检查：
- `02-index/test-map.md`
- 对应模块或流程文档中的 `Tests`
- evidence 文档中的 `Related Tests`

#### Rename Or Move

必须检查所有路径引用：
- 模块文档
- 流程文档
- evidence 文档
- review 文档中的路径引用

## Trigger Matrix

以下变化应触发哪些文档重审。

### A. 核心实现文件变化

至少重审：
- 对应模块文档
- 对应 evidence 文档

如果行为变化明显，再重审：
- 相关 flow 文档
- test-map

### B. 类型或接口变化

至少重审：
- 模块文档中的 `Core Types`
- `Dependencies`
- `Key Behaviors`
- 相关 flow 文档

### C. 入口变化

必须重审：
- inventory entrypoints
- index routes/symbols
- 相关 flow 文档
- 相关 risk 文档

### D. 外部调用变化

必须重审：
- flow 文档中的 `External Calls`
- module 文档中的 `Data Read/Write`
- risk 文档
- ops 文档

### E. 失败处理变化

必须重审：
- flow 文档中的 `Failure Paths`
- `Compensation / Retry`
- `User Visible Failures`
- 风险和 review 文档

## Update Rules

### Rule 1: Prefer Local Edits

优先只改受影响章节，不要重写整篇文档。

### Rule 2: Preserve Verified Content

未受影响且仍有效的内容应保留。

### Rule 3: Replace Stale Evidence

如果 evidence 引用已失效：
- 更新到新位置
- 或删除并补新证据

不要保留已不适用的旧 evidence。

### Rule 4: Record Uncertainty Explicitly

如果 diff 无法说明完整行为变化，应：
- 在对应文档加 `Open Questions`
- 在 `09-review/open-questions.md` 记录缺口

### Rule 5: Review Before Done

增量更新完成后，至少进行一次轻量 review，再标记相关任务为完成。

## When To Escalate

遇到以下情况时，不应仅做普通增量更新：

1. 入口系统整体重构
2. 模块边界大范围变化
3. 路由、任务调度或事件流改造导致索引全面失效
4. 大量 evidence 同时失效
5. 无法判断旧文档是否仍可信

此时应升级为以下动作之一：
- 局部 inventory 重建
- 局部 index 重建
- 模块批次重跑
- flow 批次重跑

## Suggested Incremental Task Shapes

建议使用以下任务粒度：

### Small

适用于：
- 单文件修复
- 测试补充
- 配置键微调

输出：
- 1 个模块文档局部更新
- 1 个 evidence 文档局部更新

### Medium

适用于：
- 单模块行为变化
- 单流程步骤变化
- 单入口变化

输出：
- 1 到 2 个模块文档更新
- 1 个 flow 文档更新
- 1 次 review 记录

### Large

适用于：
- 子系统级重构
- 多入口联动变化
- 大量路径迁移

输出：
- 一批 module/flow/evidence 文档更新
- 更新 dashboard
- 新 snapshot

## Required Outputs For Incremental Update

每次增量更新至少要产出以下之一：

- 被修改的 module doc
- 被修改的 flow doc
- 被修改的 evidence doc
- 新增或更新的 review 项

通常还应更新：
- `00-meta/status-dashboard.md`
- `00-meta/progress.json`
- `10-snapshots/` 下的新快照

## Sanity Checks After Update

增量更新后至少检查：

1. 更新的结论是否仍有 evidence 支撑
2. 路径引用是否仍然存在
3. 测试引用是否仍有效
4. 是否遗漏了相关 flow 或 module 文档
5. `status-dashboard.md` 是否反映了当前状态

## Anti-Patterns

不要这样做：

- 因为看不懂 diff 就重写整个 wiki
- 只改模块文档，不改 evidence
- 只改 evidence，不改已经过时的结论
- 在没有验证的情况下假设行为“应该没变”
- 忽略文件重命名带来的路径失效

## Bottom Line

增量更新的关键不是“改得快”，而是“改得准”。

只有当代码变化被正确映射到模块、流程和 evidence，`coder-llm-wiki` 才能持续保持可信。 
