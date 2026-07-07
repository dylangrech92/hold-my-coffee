---
name: scalability-stability
description: Use when designing or reviewing anything that holds state, crosses a process boundary, handles failure or untrusted input, schedules work, or must survive restarts, retries, upgrades, and growth.
---

# Scalability, Stability & Security

Stable systems share one property: **their state is always derivable and their failures are always visible.** Scale is not the hard part — most systems die of drift and silence long before they die of load.

## State

- **Shared state needs one durable source of truth.** Any process may die between any two lines, and any instance can be one of many. State that lives only in memory yet is shared across calls, instances, or restarts *will* drift — back it with a durable store and treat that store, not the copy in memory, as authoritative. If reading from the store feels too expensive to do freely, that's a data-model problem to fix, not a reason to let memory become the system of record.
- **One source of truth per concept.** Every mirror, cache, and copy is a reconciliation bug on a timer. Caches must be disposable: deletable at any moment with zero correctness impact.
- **Make invalid states unrepresentable at the most structural level you have.** A type, an enum, a constructor invariant, a database constraint (NOT NULL, UNIQUE) — each kills a whole bug class outright, which beats any runtime check hunting the bug down after the fact. Application-level validation is the fallback for rules you couldn't encode structurally.
- **Represent time unambiguously.** Timezone-aware through one shared authority (UTC end to end is the usual answer). A timezone-less timestamp silently corrupts data the first time it crosses a boundary.

## Failure

- **Loud or it didn't happen.** Every failure either bubbles up or is logged with diagnostic context. An error requiring archaeology equals no error. Design for observability from day one — logs, metrics, traces are not add-ons.
- **Crash beats corrupt.** A process that dies is restartable; a process that continues on bad state spreads it to everything it touches.
- **Gates fail closed.** Anything protective — permission checks, approval gates, policy blocks — denies when the gate itself faults. A gate that fails open is a hole with paperwork.
- **No silent data loss, ever.** Timeouts, truncations, drop-oldest, "best effort" — all forbidden without explicit owner approval. Legitimate work can run for hours; killing it on a timer is destroying user data on a schedule.
- **Retries require idempotency.** Every mutation must be safe to repeat before anything is allowed to repeat it. Idempotent, self-no-op steps are what make the one-common-path design safe under failure.
- **No fallback parsing of malformed input.** A lenient fallback masks the producer's bug today and creates a silent failure mode tomorrow. Reject loudly; fix the producer.

## Security

- **Validate at trust boundaries; trust no client-supplied data.** Escape/encode output for the context it lands in — parameterized queries, never string-concatenated queries or commands.
- **Least privilege everywhere.** Every credential, process, and service account gets the minimum it needs. Default-deny; explicit allow-lists over deny-lists.
- **Never roll your own crypto or auth.** Vetted, boring libraries only. Auth, session, and navigation-guard code gets the strictest review on the surface.
- **Secrets never land in code, logs, or version control.** Log security-relevant events; never log secrets or PII.
- **Internals never leak outward.** Errors log rich server-side, return generic client-side. A stack trace in a response is a security finding, not a debugging convenience.
- **Dependencies are attack surface.** Add them reluctantly (the ladder in `software-development.md`), pin them, audit them.

## Lifecycle

- **Signal ready only when you are.** If anything downstream can hand you work the moment you exist — a load balancer, a supervisor, a caller — come up in an honest "starting" state and flip to "ready" only when you truly are (a network service, for instance, binds its port first, answers with an honest "starting", and flips readiness last). Claim readiness early and every request in the gap is a failure.
- **A change to the shape of persisted data ships with its migration.** Whatever you persist to — a database, files on disk, saved documents — the shape change, the code that moves existing data forward, and any fixtures or seed data ship together, in one unit. A new field with no backfill shows users a hole where their data should be. One-shot migrations get removed after they run.
- **Data has a lifecycle.** Any store that only grows is a leak. Deletion must actually delete — shadow copies, indexes, reclaimable space included. "Deleted" data that remains readable is a broken promise.
- **Upgrades are a feature.** New code meets old data on every user's machine. Test the upgrade path like a feature, because it is one.
- **Before any destructive environment operation** — environment recreate, storage/volume wipe, re-init, "cleanup" — verify the irreplaceable files (keys, secrets, databases) live on storage that survives it.

## Simplicity Under Load

- **Boring scales.** A table, a queue, a scheduled job — proven parts, in that order of preference. Complex systems that work evolve from simple systems that worked. Engineering cost dominates infra cost; pick the architecture that needs fewer specialists.
- **Name the ceiling instead of building past it.** Solve today's load; document the known ceiling and upgrade path where you took the shortcut (`# lean: global lock; per-account locks if throughput matters`). Speculative scale-engineering is bloat that also happens to be untested.
- **No artificial throttles, delays, or budget caps** as complexity management. They mask the real constraint and punish legitimate use. Natural exit conditions only.
- **Continuous work runs continuously.** If a background process needs a cooldown to be tolerable, the process is wrong, not the schedule.

## Anti-Patterns

- **Cross-instance in-memory state** — works until the second instance, the first crash, or the first deploy.
- **Liveness by proxy** — a valid reference to a dead thing passes every check and delivers to nowhere.
- **Swallow-and-DEBUG-log** — functionally identical to catch-and-ignore for anyone operating the system.
- **Drop-oldest buried in a queue or buffer** — data loss placed where nobody will look for it.
- **Cleanup by deletion of unknown files** — keys, secrets, and databases die in "cleanup". If you didn't create it, you don't delete it.
- **Fail-open protective checks** — the one day the gate faults is the one day it mattered.
