---
name: task-breakdown
description: Use when turning an approved approach into executable work — slicing a feature into tasks, ordering a migration, planning parallel workstreams, or writing tickets.
---

# Task Breakdown

A breakdown is good when every task leaves the system working and every done-condition fits in one sentence. Breakdown quality decides review size, rollback cost, and whether parallel workers collide.

## Slicing Rules

1. **Vertical slices only.** Each task ships observable behavior through the whole stack. Horizontal slicing — a whole layer at a time ("all models, then all handlers, then all UI") — produces weeks of nothing-works and one unreviewable merge.
2. **One-sentence done-condition** — observable, checkable. Can't state it? The task is too big or too vague; split it.
3. **One ticket per task**, carrying context (why), acceptance criteria (what), and evidence-of-done (proof). A future stranger — human or agent — must be able to execute it without the conversation that spawned it.
4. **Multi-part features repeat the full pipeline per part.** Never batch parts through one pass — review cost is superlinear in diff size, and batching hides which part broke what. The pipeline is sized to each part's risk; batching is the sin, not lightness.
5. **Riskiest first.** Unknowns, integrations, and third-party surfaces go first — that's where the plan dies, and you want it to die cheap. Polish last.
6. **Every task ends green.** Sequence so the system builds, boots, and passes after each task. A plan with a broken-in-the-middle phase is a plan to get interrupted in the middle.
7. **Boundary contracts precede scoped tasks.** Work spanning a process/service/interface boundary gets the contract (protocol, data flow, lifecycle model) decided in writing before tasks are cut — implicit boundaries are how implementations come back wrong.

## Code Migrations

Moving or rewriting live functionality follows one iron sequence, per unit: **copy → wire → verify → delete.**

- Never leave a hollow shell (a stub returning `''`, a passthrough wrapper). One discovered shell makes every other "done" in the migration untrustworthy.
- Never park on peripheral work while the critical path is a stub. Finish the spine first.
- The delete lands in the same task as the wire-up. "Remove the old path later" is how two sources of truth are born.

## Parallel Work

- Partition by file ownership. Shared-file changes get sequenced, not parallelized.
- Workers proceed on best judgment and batch questions for the end; only a true owner-level fork blocks.
- Each worker commits only its own files. Foreign changes in the tree are someone else's live work — untouchable.

## Anti-Patterns

- **Horizontal slicing** — nothing works until everything works; review and rollback become all-or-nothing.
- **Mega-tickets / "misc fixes"** — unreviewable, unrevertable, hides scope creep by design.
- **Scope smuggling** — work in no ticket is work nobody approved and nobody will remember.
- **Hollow-shell migration** — the stub lies about progress; trust in the whole effort collapses with it.
- **Plan frozen against reality** — when implementation contradicts the plan, stop and flag; don't silently rewrite either side to match the other.
