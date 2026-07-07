---
name: software-development
description: Use when designing, writing, or modifying code — any feature, fix, refactor, or removal — from the first line of design to the last line of test.
---

# Software Development — Design, Build, Test

Code is a liability that occasionally pays rent. The craft is threefold: understand before touching, make correctness structural, leave zero residue. Optimize for the reader — code is read far more often than it is written.

**The codebase is precedent for style, never proof of quality.** Inherit its names, idioms, and structure; never replicate its defects, band-aids, or bloat because they're already there. When the established pattern conflicts with these standards, flag it and propose — don't silently propagate it, and don't silently "fix" it out of scope.

## Understand Before You Code

No edit before you can answer with evidence:

1. **What executes at runtime** — in what order, with what data? Import graphs and greps are not behavior.
2. **What exercises this path** — which tests, flows, users? If nothing does, that's a finding.
3. **What breaks for old data and upgrade paths?** Code meets databases written by every previous version of itself.
4. **What surprises a maintainer three months out** — implicit contracts, timing assumptions, adjacent services?
5. **What are the silent side effects** — the write you didn't know fired, the cache you didn't know existed?

And read the graveyard: deleted code died for a reason; know it before resurrecting the approach.

## Design

- **Climb the ladder; stop at the first rung that holds.** Does it need to exist at all? → standard library? → platform/framework feature (DB constraint over app code, CSS over JS)? → already-installed dependency? → one line? → only then, minimum new code. Never add a dependency for what a few lines cover.
- **Deep modules.** Simple interface, powerful implementation. If you can't explain the interface in one sentence, the seams are wrong. Push complexity down into few places; eliminate special cases structurally instead of branching around them.
- **Public API only.** Never build on a library's private internals — every future fix becomes a band-aid on a wound you inflicted. If the public API can't do it, that's a design signal, not a challenge.
- **Minimum effective dose, with the leverage exception.** Smallest change wins — unless one change a level up eliminates the entire problem class; then take the top. Either way: propose scope changes, never assume them.
- **Variance into data, not control flow.** One common path whose steps self-no-op when not needed. Don't guard with `if <needed>: do_it` — make the operation harmless when unneeded and run it unconditionally. The second `if X: do A` beside `if Y: do A'` announces a chokepoint: one call every similar action flows through, iterating a list of transforms, each a no-op when absent. The third variant then touches one list, never the call site.
- **The right abstraction, or none.** Abstract when the second real sibling exists, never in anticipation — duplication is cheaper than the wrong abstraction. No interface with one implementation, no factory for one product, no parameter, flag, or config "in case".
- **Contracts before tests.** A typed signature, an abstract method, a single return type, a DB constraint — each makes a bug class unrepresentable, which beats any test asserting the bug didn't happen. Interfaces are rigid: abstract methods force what genuinely varies; lock what must not vary (`final` or the language's equivalent). Drift enters through loose interfaces.
- **No state outside the database.** In-process state carried across calls or instances is a second source of truth that drifts. Rebuild from the store; any process may die at any line.

## Build

- **Zero residue.** Rename = every caller updated in the same commit, no alias. Remove = file, imports, callers, tests, config, docs — all gone, same commit. No commented-out "for reference" code; version control is the archive, not the codebase.
- **No adapters, shims, polyfills, or forwarding wrappers — ever.** A function that only calls another function is bloat in a compatibility costume. Fix the callers.
- **Loud errors.** Catch to add context and re-raise, or log with enough context to diagnose — one of the two, always. Exceptions for failures, never sentinel returns (`None`, `-1`, `False`-as-error). One return type per function.
- **The failure path is a feature.** Whatever consumes your output — user, service, or model — must be told when a step failed. A silently skipped step poisons every decision downstream of it.
- **Private by default; expose the minimum.** Everything starts private; promote only when the contract requires it. No preemptive public methods; APIs return the minimum the caller needs — never internal IDs, full objects, or adjacent data unasked.
- **Small units, shallow nesting.** A function does one thing at one level of abstraction; if reading it requires scrolling, split it. Guard clauses and early returns over deep if-trees. Five parameters is the ceiling — past that, the group becomes a first-class object.
- **No magic literals.** Every inline string or numeric default becomes a named constant, scoped where it's used — class constant if one class needs it, the shared parent if siblings do.
- **Names are inherited, not invented.** Use the exact existing names — the owner's, the codebase's, the tool's. Classes are nouns, methods verbs, booleans read as questions (`is_`, `has_`, `can_`). A new umbrella name for an existing thing is a translation layer everyone pays forever.
- **Comment the why, never the what.** One or two sentences of intent per function and class. If a comment must explain what the code does, rewrite the code. No boilerplate restating the signature. Comments ship publicly — internal-process details never belong in them.
- **Reuse before create.** Search first; adapt an existing unit when it serves. Create only when nothing covers the behavior.

## Test

- **Feature tests on the real hot path, zero mocks.** Regressions live *between* steps; mocks amputate exactly the seams where bugs breed. Mock only what you genuinely cannot run.
- **Proportionality.** Major rework earns feature tests; a one-line relocation doesn't. Test only behavior a contract cannot express — never restate a signature the interface already enforces.
- Full doctrine — thresholds, environments, which tests to delete — lives in `qa-principles.md`.

## Definition of Done

Committed tree (not working tree) verified: runs end-to-end, feature-tested, both branch sides covered, zero residue, migrations + seeders shipped, docs updated, reviewed. Anything less is "in progress."

## Red Flags — Stop and Retrace

- You're writing a wrapper to avoid updating callers.
- You're adding a parameter, flag, or field "in case".
- Your diff is net-positive LOC and you can't defend every added line.
- You're editing the spec or the test to match the code.
- You're copying a nearby pattern without being able to defend it from first principles.
- The current edit doesn't serve the original ask in one sentence — that's drift; back out to the last decision that did.
