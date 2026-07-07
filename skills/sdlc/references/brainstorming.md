---
name: brainstorming
description: Use when starting any creative or structural work — a new feature, capability, redesign, or significant change — before any spec or code exists, while ideas are still cheap to kill.
---

# Brainstorming

Kill bad ideas while they cost nothing. The best feature is often the one not built; the best change is often upstream of where the request points. When in doubt, ask — asking costs a sentence; unwanted work costs a review cycle plus a revert.

## The Sequence

1. **Problem first.** Who hurts, when, doing what? A request that arrives as a solution gets walked back to its problem — the stated solution is one candidate, not the spec.
2. **Apply the product filter.** Every product needs one sharp question every idea must pass (e.g. "does this make a new user's first 30 minutes better?"). No filter defined? Defining one is the first deliverable. Idea fails it? Stop.
3. **Respect knowledge asymmetry — both directions.** The owner holds context no document captures; your technical findings are evidence they don't have. If the spec contradicts your findings, surface the conflict and ask — never silently bend the spec toward your code, never silently comply with what you believe is wrong.
4. **Check the graveyard.** Search history and decisions for approaches already tried and removed — and why. Re-proposing a deliberately killed feature without addressing why it died burns trust. Chesterton's fence applies to absences too.
5. **Try deletion and upstream moves before addition.** Can the problem vanish by removing something? Can one change at a higher level eliminate the whole class? One change at the top beats N patches at the leaves — propose it, never assume it.
6. **Collapse convergent shapes into data, not branches.** When two features have evolved the same shape, unify them — variance goes into a list/config/table, not if-branches. Test: fewer concepts, no case-switching.
7. **Research how others solved it.** Harvest their failure modes and scaling ceilings. You are not the first.
8. **Produce real alternatives.** At least two genuinely different approaches, each priced in code, complexity, and maintenance. Recommend one. A single option is a fait accompli, not a decision.

## Output — one page, no more

**Problem** (one sentence, user terms) · **Chosen approach** (the why, as an evidence chain, not adjectives) · **Rejected alternatives** (why each lost) · **Non-goals** (scope is a feature) · **Open questions** (batched; only true forks block).

Hard gate: structural or architectural work gets explicit owner approval on this page **before any implementation code exists**.

## Anti-Patterns

- **Solutioneering** — committing to the first idea and reverse-justifying it. Generate rivals and let them fight.
- **"While we're at it"** — scope smuggled in without consent. Propose; don't expand.
- **Future-proofing** — no field, column, or abstraction "in case". Test: needed today, for a real use?
- **Designing for imagined scale** — solve today's load; name the ceiling and the upgrade path instead of building past it.
- **Novelty-driven choices** — the boring, proven option wins unless new tech beats it on evidence.
- **Judging by your own setup** — judge by the product vision and the whole audience, not what you personally run.
- **Deciding the owner's forks** — "I assumed you'd want…" is taking a decision that isn't yours. Ask.
