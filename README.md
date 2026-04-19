# LLM Wiki

This directory stores a long-running, evidence-backed knowledge base for the repository.

## Core Rules

- Write every important conclusion with source evidence.
- Keep facts and interpretation separate.
- Prefer incremental updates over full rewrites.
- Record open questions instead of guessing.

## Start Here

If you are starting or resuming work, read these files first:

1. `coder-llm-wiki/00-meta/project-charter.md`
2. `coder-llm-wiki/00-meta/workflow-contract.md`
3. `coder-llm-wiki/00-meta/quality-gates.md`
4. `coder-llm-wiki/00-meta/status-dashboard.md`
5. `coder-llm-wiki/00-meta/opencode-dispatch-checklist.md`

These files play different roles:
- `project-charter.md`: defines goals and non-goals
- `workflow-contract.md`: defines phase inputs, outputs, and exit criteria
- `quality-gates.md`: defines what counts as complete
- `status-dashboard.md`: shows current progress, blockers, and next steps
- `opencode-dispatch-checklist.md`: operator runbook for day-to-day execution

## Structure

- `00-meta/`: project rules, progress, queue, prompts, glossary
- `01-inventory/`: repo map, stack, entrypoints, module candidates
- `02-index/`: routes, models, jobs, symbols, tests
- `03-modules/`: one document per module
- `04-flows/`: one document per important flow
- `05-data/`: schema, cache, event, storage notes
- `06-ops/`: run, build, deploy, monitor, recovery notes
- `07-risks/`: fragile areas, technical debt, hidden constraints
- `08-evidence/`: file and line references backing each doc
- `09-review/`: conflicts, questions, human review backlog
- `10-snapshots/`: per-task checkpoints and resumable state

## Workflow

The recommended workflow is now phase-driven:

1. `Initialize`
2. `Inventory`
3. `Index`
4. `Prepare Module Queue`
5. `Module Analysis`
6. `Lightweight Review`
7. `Flow Planning`
8. `Flow Analysis`
9. `Cross Review`
10. `Snapshot`
11. `Incremental Update`

See `coder-llm-wiki/00-meta/workflow-contract.md` for the full contract.

## How To Work

### New repository analysis

1. Build inventory first.
2. Build index docs from real entrypoints.
3. Split module work into queue-sized tasks.
4. Write module docs with evidence and open questions.
5. Review before marking tasks done.
6. Plan and write high-value flow docs.
7. Run cross-review and write a snapshot.

### Resume an interrupted run

1. Read `00-meta/progress.json`.
2. Read `00-meta/task-queue.json`.
3. Read the latest file under `10-snapshots/`.
4. Read `00-meta/status-dashboard.md`.
5. Resume from the current phase instead of rescanning everything.

### Update after code changes

1. Read the current diff or changed files.
2. Map changes to affected modules and flows.
3. Update only impacted docs and evidence.
4. Re-run lightweight review.
5. Write a new snapshot when the update batch is done.

See `coder-llm-wiki/00-meta/incremental-update-policy.md` for the detailed policy.

## State Files

The main state files are:

- `00-meta/progress.json`: current phase, batch, coverage, blockers, snapshot pointer
- `00-meta/task-queue.json`: task lifecycle and review state
- `00-meta/status-dashboard.md`: human-readable summary
- `10-snapshots/`: resumable checkpoints

Treat these as operational state, not optional notes.

## Definition Of Done

A wiki task is not done just because text exists. It is done when:

1. The target document is written.
2. Key conclusions have evidence.
3. Uncertainty is recorded in review docs.
4. Queue and progress state are updated.
5. The relevant quality gates pass.

## Operating Principle

This wiki should behave like a lightweight knowledge system, not a loose collection of summaries.
