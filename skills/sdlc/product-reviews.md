---
name: product-reviews
description: Use when evaluating the product as a whole — before a release, after a feature lands, or on a recurring cadence — to judge what a customer will actually experience.
---

# Product Reviews

One question: **what does a customer actually experience?** Not what the code does, not what the tests assert — what a person sees, waits for, and feels. There is no substitute for using the product. Customer trust is built slowly through honesty and quality, and spent instantly — a bad day one kills retention permanently.

## The Method

1. **Become a customer, not the author.** Fresh environment, first-run state (no account, no history), empty data, default config, modest hardware. Warm dev setups hide the whole cold-start class of failure — "flawless in dev, dead blank screen on every fresh install" is the classic, because only fresh installs pay the cold-boot cost.
2. **Live the first 30 minutes.** Install → first boot → first meaningful success. Time it. Every stumble in this window weighs 10× — it's the only window every customer is guaranteed to experience.
3. **Walk the golden paths end-to-end, then the ugly ones.** Empty states, error states, the slow machine, the flaky network, the impatient double-click, walking away mid-operation. The demo path is rehearsed; customers improvise.
4. **Judge by the vision and the whole audience** — not your own setup, environment, or habits. The product filter (defined in `brainstorming.md`) is the yardstick, not personal taste.
5. **Audit the words.** Error messages must say what happened and what to do next, in product vocabulary. Internal jargon, raw exception text, or invented names in the UI is a defect, same severity as a logic bug.
6. **Verify the release story.** For each change: one sentence on what changed *for the user*. No such sentence → why did it ship? Overpromising → the release notes are lying. Release notes are developer-honest, not marketing fluff.
7. **Check the waits.** Every long operation tells the truth about what it's doing; an operation that blocks must *visibly* block, not pretend to be done.

## Verdict Contract

A written verdict: **ship / don't ship**, with the blocking findings. Findings filed as tickets immediately — a finding in your head is a finding lost — and described as the customer experiences them ("uploading a file lets me keep chatting, then answers without the file's content"), not as the developer explains them.

## Anti-Patterns

- **Developer-eyes testing** — you know where not to click. Customers don't.
- **Works-on-my-machine sign-off** — your machine is warm, fast, seeded, and configured. Theirs is none of those.
- **Demo-path-only review** — the rehearsed path is guaranteed to work; it carries no information.
- **Green-suite complacency** — suites prove the code you tested; the customer runs the product you shipped.
- **Polish as optional** — to a customer, a rough edge and a bug are the same thing: evidence of carelessness.
- **Reviewing with seeded/ideal data** — empty and messy data are the customer's default, not the edge case.
