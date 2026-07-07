---
name: qa-principles
description: Use when deciding what to test, how to test it, building test environments or harnesses, interpreting test results, or verifying work before claiming it done.
---

# QA Principles

QA answers one question with evidence: **does the shipped thing work for a real user?** A green suite is a precondition for confidence, never the source of it. No unit test, however elegant, substitutes for exercising the product.

## The Three Tiers — Never Blurred

- **Feature tests** — in-repo, deterministic, real stack, zero mocks. *Does this mechanism work end-to-end?*
- **Scenario observation** — automated runs mimicking real users, pass/fail. *Does the product behave when used naturally?*
- **Benchmarks** — scored quality runs. *How well does it perform, trending over time?*

Each answers a question the others cannot. A feature test asserting quality, a scenario asserting string formats, a benchmark used as a gate — all category errors.

**The harness wraps the product — never the other way around.** Production code is never modified to accommodate a test: no skip-flags, no test-only endpoints, no backdoors. An endpoint no real consumer uses is dead weight plus attack surface, built to flatter a suite.

## Feature Testing

- **Real hot path, zero mocks.** Regressions live *between* components; mocks amputate exactly those seams. Mock only what you genuinely cannot run. Coverage percentage is not a quality metric — path reality is.
- **Assert downstream effects** — the row written, the message emitted, the file produced — not return values.
- **Reproduce bugs as tests before fixing.** The failing test is the proof your fix fixes.
- **Both sides of every threshold.** The branch your test data never triggers is where the production 500 waits. Shape data to cross the boundary, not just approach it.
- **Build test state from production sources** — the real schema file, the real seeders. Hand-copied DDL and fixtures rot silently, then test a database that no longer exists.
- **Portability.** No hardcoded local paths, no environment assumptions. A test that only passes on its author's machine is a rumor.
- **Proportionality.** Major work earns feature tests; trivial mechanical changes don't. Test-line inflation offsetting production-line reduction is bloat with a green badge.
- **Delete bad tests.** Gatekeeper tests (a constant contains a substring), brittle tests (break on every touch, no behavior change), tests of artifacts no user receives — all negative value. Deleting them is QA work.

## Scenario Observation

- **Mimic a real user, or measure nothing.** Implicit natural prompts, real workflows, no steering toward the mechanism under test — a user can't do that, so the test mustn't.
- **Judge behavior, not strings** — latency, wrong actions, wasted steps, off-topic drift; never literal output formats a real user wouldn't notice.
- **Poll, don't snapshot.** One-shot reads of async results are the top source of false failures. Poll with a deadline.
- **Instrument before you run.** If the environment tears down after the run, evidence not captured live is gone forever.
- **Weight critical steps.** An average lets one catastrophic failure hide inside a passing score; gate-worthy steps fail the whole scenario alone.
- **A failed run gets a diagnosis, not a rerun.** Rerun-until-green launders bugs into noise.

## Test Environments — Hard Rules

- **A live instance is never a test target.** Disposable environments provisioned for the purpose, torn down after. Instances holding real data are off-limits, unconditionally.
- **Every automated test agent gets explicit DO-NOT constraints:** no credential resets, no auth/vault mutation, no destructive shell operations, no deleting files it didn't create. An unstated boundary is a boundary that will be crossed.
- **Blocked ≠ licensed.** A failed login or a locked resource is a result to report, never an obstacle to defeat.

## Verifying "Done"

- **Verify the committed tree, not the working tree.** The strongest evidence: a fresh environment built from the commit, reaching healthy, exercising the change.
- **Claiming done requires having watched it work** — the output, the response, the row, the pixel. "Should work" is not a QA verdict.
- **Deadlines don't change the epistemics.** Under pressure, deliver a fast honest risk statement: what is known, what isn't, with evidence for both. Shipping with a known unknown is the owner's call — never make it silently on their behalf.

## Anti-Patterns

- **Mock theater** — green tests over amputated seams verify a product that doesn't exist.
- **Coverage worship** — 100% coverage of the wrong assertions is 0% confidence at full maintenance cost.
- **Flaky tolerance** — a flaky test is a real bug with a probability attached, being trained into background noise.
- **Test-only backdoors in prod** — the suite passes; the product carries a scar and an attack surface.
- **Skip-flags to appease infra** — papering over a real ordering/dependency bug and shipping the paper.
- **Rerun-and-hope** — turns your instrument into a slot machine.
