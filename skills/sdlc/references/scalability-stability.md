---
name: scalability-stability
description: Use when designing or reviewing anything that holds state, crosses a process boundary, handles failure or untrusted input, schedules work, or must survive restarts, retries, upgrades, and growth.
---

# Scalability, Stability & Security

Stable systems share one property: **their state is always derivable and their failures are always visible.** Scale is not the hard part — most systems die of drift and silence long before they die of load.

## State

- **The database is the only truth.** Any process may die between any two lines. In-memory state carried across calls, instances, or restarts *will* drift — rebuild from the store on every cycle. If rebuilding feels expensive, that's a schema/query problem, not a license to cache truth in RAM.
- **One source of truth per concept.** Every mirror, cache, and copy is a reconciliation bug on a timer. Caches must be disposable: deletable at any moment with zero correctness impact.
- **Constraints live in the database; contracts live in types.** NOT NULL, UNIQUE, foreign keys, typed signatures — each makes a bug class unrepresentable. Application-level discipline is the fallback for rules you failed to encode structurally.
- **All time is timezone-aware UTC** through one shared utility. Naive datetimes silently corrupt data.

## Failure

- **Loud or it didn't happen.** Every failure either bubbles up or is logged with diagnostic context. An error requiring archaeology equals no error. Design for observability from day one — logs, metrics, traces are not add-ons.
- **Crash beats corrupt.** A process that dies is restartable; a process that continues on bad state spreads it to everything it touches.
- **Gates fail closed.** Anything protective — permission checks, approval gates, policy blocks — denies when the gate itself faults. A gate that fails open is a hole with paperwork.
- **No silent data loss, ever.** Timeouts, truncations, drop-oldest, "best effort" — all forbidden without explicit owner approval. Legitimate work can run for hours; killing it on a timer is destroying user data on a schedule.
- **Retries require idempotency.** Every mutation must be safe to repeat before anything is allowed to repeat it. Idempotent, self-no-op steps are what make the one-common-path design safe under failure.
- **No fallback parsing of malformed input.** A lenient fallback masks the producer's bug today and creates a silent failure mode tomorrow. Reject loudly; fix the producer.

## Security

- **Validate at trust boundaries; trust no client-supplied data.** Escape/encode output for the context it lands in — parameterized queries, never string-built SQL or shell.
- **Least privilege everywhere.** Every credential, process, and service account gets the minimum it needs. Default-deny; explicit allow-lists over deny-lists.
- **Never roll your own crypto or auth.** Vetted, boring libraries only. Auth, session, and navigation-guard code gets the strictest review on the surface.
- **Secrets never land in code, logs, or version control.** Log security-relevant events; never log secrets or PII.
- **Internals never leak outward.** Errors log rich server-side, return generic client-side. A stack trace in a response is a security finding, not a debugging convenience.
- **Dependencies are attack surface.** Add them reluctantly (the ladder in `software-development.md`), pin them, audit them.

## Lifecycle

- **Boot honestly.** Infrastructure publishes your port the instant the container starts; bind first, serve an honest "starting" state, flip readiness when true. Bind late and every early request is a connection reset.
- **Schema migrations ship complete:** schema change + data backfill + seeder update, together. A new column without a backfill shows users a hole where their data should be. One-shot migrations get removed after they run.
- **Data has a lifecycle.** Tables and directories that only grow are leaks. Deletion must actually delete — shadow copies, indexes, reclaimable space included. "Deleted" data that remains readable is a broken promise.
- **Upgrades are a feature.** New code meets old data on every user's machine. Test the upgrade path like a feature, because it is one.
- **Before any destructive environment operation** — container recreate, volume prune, re-init, "cleanup" — verify the irreplaceable files (keys, secrets, databases) live on storage that survives it.

## Simplicity Under Load

- **Boring scales.** A table, a queue, a cron — proven parts, in that order of preference. Complex systems that work evolve from simple systems that worked. Engineering cost dominates infra cost; pick the architecture that needs fewer specialists.
- **Name the ceiling instead of building past it.** Solve today's load; document the known ceiling and upgrade path where you took the shortcut (`# lean: global lock; per-account locks if throughput matters`). Speculative scale-engineering is bloat that also happens to be untested.
- **No artificial throttles, delays, or budget caps** as complexity management. They mask the real constraint and punish legitimate use. Natural exit conditions only.
- **Continuous work runs continuously.** If a background process needs a cooldown to be tolerable, the process is wrong, not the schedule.

## Anti-Patterns

- **Cross-instance in-memory state** — works until the second instance, the first crash, or the first deploy.
- **Liveness by proxy** — a valid reference to a dead thing passes every check and delivers to nowhere.
- **Swallow-and-DEBUG-log** — functionally identical to `except: pass` for anyone operating the system.
- **Drop-oldest at the transport layer** — data loss placed where nobody will look for it.
- **Cleanup by deletion of unknown files** — keys, secrets, and databases die in "cleanup". If you didn't create it, you don't delete it.
- **Fail-open protective checks** — the one day the gate faults is the one day it mattered.
