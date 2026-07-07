---
name: technical-reviews
description: Use when reviewing code, a pull request, a diff, or an architecture — and when receiving review feedback on your own work.
---

# Technical Reviews

A review is an evidence-gathering exercise, not a reading exercise. Its output is verified findings, not impressions. A review that never ran the code is an opinion with formatting.

## Reviewing

1. **Review what ships: the committed tree.** The working tree lies — a stale index entry can pass every local gate and crash-loop in deploy. Check out the actual commit; better, build it fresh.
2. **Run it.** Execute the changed path with real inputs. Watch the failure paths fire, not just the happy one.
3. **Verify every claim.** "Tests pass", "this fixes X", version strings — all narrative until reproduced. Confident-and-wrong is the default failure mode of reviews.
4. **Read beyond the diff.** The bug lives at the seam: the caller not updated, the second branch not covered, the contract silently narrowed.

**The hunt list** — what actually gets through:

- Silent exceptions: catch-and-pass, swallow-and-debug-log, sentinel returns.
- Unwired features — built, tested, never called from anywhere real.
- Hollow wrappers and passthroughs — unfinished migrations in disguise.
- Untested branch sides on any threshold/size/count condition.
- Schema change without data migration/backfill; seeders not updated.
- Residue: dead imports, orphaned tests, commented-out code, stale config.
- Internals leaking through error responses.
- Freshly minted second sources of truth — a cache, a copy, an in-memory mirror.
- Fallback parsing that masks malformed input — hides today's bug, incubates tomorrow's silent one.

5. **Chesterton's fence, enforced.** Before flagging code as removable, find out why it exists. A deliberate feature causing trouble gets a ticket describing the conflict — never a quiet deletion. Intent outranks your regression graph.
6. **Log findings the moment you see them**, with file and line. Findings batched "for the summary" die with the session.
7. **Severity honesty; fast signal beats full sweep.** Report gate-worthy findings immediately — don't keep polling for completeness while the owner waits. Never inflate nits into blockers or bury blockers in nits.
8. **Approve at "better", block on defects.** Demand fixes for real findings; don't hold work hostage to taste. Perfectionism blocks the team more than good-but-imperfect code does.
9. **Never silence the instruments.** Suppressing a scanner finding, acknowledging an alert, skipping a failing check — all require explicit owner permission. Every time.

## Receiving Review

- **Verify before you comply.** Reviewers are wrong at roughly the same rate as authors. Reproduce the claim; refute with evidence — respectfully.
- **No performative agreement.** "Good catch!" followed by a mechanical edit you don't understand is how the second bug enters. Understand the mechanism or ask.
- **A valid finding is a gift twice:** fix the instance, then ask what let it in — that's the process fix.

## Anti-Patterns

- **Rubber-stamping** — an unread approval is a forged signature on someone else's risk.
- **Style nits as the whole review** — effort theater while the logic bug ships.
- **Reviewing the narrative, not the code** — the description is the author's hope; the diff is the fact.
- **"Looks right to me" on untested paths** — looking is not evidence. Run it.
- **Deleting what you don't understand** — ticket the conflict; quiet cleanup of deliberate features destroys trust.
- **Sitting on findings until the end** — sessions end unexpectedly; unlogged findings never existed.
