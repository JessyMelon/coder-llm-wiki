# OpenCode Dispatch Checklist

Use this as the operator runbook for a long-running repository analysis.

This checklist should be used together with:
- `coder-llm-wiki/00-meta/workflow-contract.md`
- `coder-llm-wiki/00-meta/quality-gates.md`

The contract defines phase boundaries.
The gates define what is allowed to count as complete.

## Phase 0: Initialize
- Create or verify the `coder-llm-wiki/` directory tree.
- Read `coder-llm-wiki/README.md` and `coder-llm-wiki/00-meta/project-charter.md` before starting analysis.
- Read the latest `progress.json`, `task-queue.json`, and most recent snapshot if present.
- Update `coder-llm-wiki/00-meta/progress.json` to set `phase` to the current phase.
- Clear or seed `coder-llm-wiki/00-meta/task-queue.json` with the next batch of tasks.
- Record any repository-specific naming rules in `coder-llm-wiki/00-meta/glossary.md`.
- Set `current_batch_id` and initialize coverage counters if starting a new run.

## Phase 1: Inventory
- Confirm no reliable inventory already exists before rescanning the whole repo.
- Read root documentation and top-level config files.
- Identify top-level apps, packages, services, scripts, and data directories.
- Identify runtime entrypoints such as app startup, CLI commands, workers, schedulers, or consumers.
- Write `coder-llm-wiki/01-inventory/repo-map.md`.
- Write `coder-llm-wiki/01-inventory/tech-stack.md`.
- Write `coder-llm-wiki/01-inventory/entrypoints.md`.
- Write `coder-llm-wiki/01-inventory/module-candidates.json`.
- Check against quality gates before marking complete.
- Update `progress.json` to mark `inventory` as `done`.

## Phase 2: Index
- Update `progress.json` to set `phase` to `index`.
- Build a route or command index from real entrypoints.
- Build a symbol index for core classes, functions, or schemas.
- Build a job, event, or background task index if the repository has them.
- Build a test map that links important code to tests.
- Write outputs under `coder-llm-wiki/02-index/`.
- Record indexing limitations when runtime dispatch is too dynamic to resolve confidently.
- Check index docs against quality gates before marking complete.
- Update `progress.json` to mark `index` as `done`.

## Phase 3: Prepare Module Queue
- Update `progress.json` to set `phase` to `prepare_module_queue`.
- Review `module-candidates.json`.
- Split the repository into stable module units.
- Create one queue item per module in `task-queue.json`.
- For each module task, include scope, inputs, outputs, status, priority, dependencies, and notes.
- Prefer reviewable module sizes over broad subsystem dumps.
- Mark the next module batch as ready.

## Phase 4: Module Analysis Loop
- Update `progress.json` to set `phase` to `module_analysis`.
- Pick the next module task with status `pending`.
- Mark the task as `scanning`.
- Analyze the module directory, related entrypoints, config, and tests.
- When needed, read upstream callers and downstream effects instead of documenting the module in isolation.
- Write `coder-llm-wiki/03-modules/<module>.md` from the module template.
- Write `coder-llm-wiki/08-evidence/<module>.refs.md` with file and line references.
- Write `coder-llm-wiki/09-review/<module>.questions.md` for unresolved gaps.
- Update task state through `summarizing` and `evidence-linked` as work progresses.
- Mark the task as `review-needed`.
- Repeat until the current batch is complete.

## Phase 5: Lightweight Review Loop
- Update `progress.json` to set `phase` to `lightweight_review`.
- Review each newly written module doc against the evidence doc.
- Check that the module doc states purpose, boundaries, dependencies, data interactions, risks, tests, and open questions.
- Check that at least five high-value evidence references are present when the module is non-trivial.
- Check fact vs inference separation.
- Check cross-document consistency with index docs.
- If the module doc is acceptable, mark the task as `done`.
- If the module doc is weak or inconsistent, mark the task as `blocked` or return it to `pending` with a note.

## Phase 6: Flow Planning
- Update `progress.json` to set `phase` to `flow_planning`.
- Identify the highest-value flows from entrypoints, modules, and tests.
- Create one queue item per flow in `task-queue.json`.
- Prefer business-critical, failure-prone, or heavily integrated flows first.
- Each flow task should include entry files, related modules, expected outputs, and dependency notes.

## Phase 7: Flow Analysis Loop
- Update `progress.json` to set `phase` to `flow_analysis`.
- Pick the next flow task with status `pending`.
- Mark the task as `scanning`.
- Start from real entrypoints and trace the call path.
- Capture the main path, failure paths, state changes, and external calls.
- Capture exit conditions, compensation or retry behavior, and user-visible failures when present.
- Write `coder-llm-wiki/04-flows/<flow>.md`.
- Add or update any supporting evidence docs under `coder-llm-wiki/08-evidence/`.
- Update task state through `summarizing` and `evidence-linked` as work progresses.
- Mark the flow task as `review-needed`.

## Phase 8: Cross Review
- Update `progress.json` to set `phase` to `review`.
- Compare flow docs against related module docs.
- Compare index docs against module and flow docs.
- Record factual conflicts in `coder-llm-wiki/09-review/conflict-log.md`.
- Record unresolved gaps in `coder-llm-wiki/09-review/open-questions.md`.
- Record items that need human judgment in `coder-llm-wiki/09-review/human-review.md`.
- Fix weak docs or queue them for another pass.
- Mark `review` as `done` only when all critical conflicts are either resolved or explicitly tracked.

## Phase 9: Snapshot And Resume
- Update `progress.json` to set `phase` to `snapshot`.
- After each batch, write a checkpoint file under `coder-llm-wiki/10-snapshots/`.
- Include batch id, completed tasks, current stage, outputs written, review results, and remaining blockers.
- Update `progress.json.last_snapshot` after writing the snapshot.
- On restart, read `progress.json`, `task-queue.json`, and the latest snapshot before resuming.

## Phase 10: Incremental Maintenance
- Update `progress.json` to set `phase` to `incremental_updates` when running against new code changes.
- Read the current git diff or changed files.
- Map code changes to affected modules and flows.
- Update only the impacted wiki docs and evidence files.
- Append any new uncertainty to the review docs.
- Keep old evidence only if it still matches the current code.
- If a change cannot be mapped confidently, fall back to index or inventory repair before editing many docs.

## Status Vocabulary
- `pending`: not started
- `scanning`: source discovery in progress
- `summarizing`: writing the main document
- `evidence-linked`: evidence file updated
- `review-needed`: ready for review
- `done`: complete for now
- `blocked`: cannot continue without clarification or conflict resolution

## Required Task Fields
- `id`
- `type`
- `scope`
- `status`
- `owner`
- `priority`
- `depends_on`
- `inputs`
- `outputs`
- `review_result`
- `notes`
- `last_updated_at`

## Operator Notes
- Do not treat generated text as final until evidence is linked.
- Do not merge facts and interpretation into the same section when certainty is low.
- Prefer rerunning a small task over rewriting a large batch.
- Keep the queue small enough to review regularly.
- Do not mark tasks as `done` until the relevant quality gates pass.
- Prefer updating progress and queue state immediately after each task, not in batches.
