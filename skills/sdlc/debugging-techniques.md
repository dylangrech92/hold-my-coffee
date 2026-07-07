---
name: debugging-techniques
description: Use when behavior contradicts expectation — a bug, crash, regression, flaky test, wrong output, silent failure, or performance mystery — before proposing or writing any fix.
---

# Debugging Techniques

Debugging replaces narrative with evidence. You are done when you can explain the failure mechanism end-to-end and prove it — not when the symptom stops.

## The Method

1. **Reproduce first.** No reproduction, no fix. A fix you can't watch fail is a guess. If it fails "sometimes", the reproduction rate is your first measurement.
2. **Read the actual artifact** — the real error text, log lines, rows, request/response exchange. Never reason from what the error "probably" says; never trust a version file, status page, or comment over the running system.
3. **Measure, don't eyeball.** Count the operations — requests, allocations, iterations. Time the boot. Probe the real state — a port, a file, a counter. A number ends arguments that impressions start.
4. **Trace the behavioral path, not the import path.** What actually executes, in what order, with what data? "Nothing imports this" is not evidence nothing reaches it.
5. **One written hypothesis at a time.** State it, state the observation that would falsify it, run the check. If the fix works, you must be able to say *why* — a fix that works for unknown reasons is an unexploded bug.
6. **Bisect.** Halve the search space: across commits, code, data, environments. Fresh environments expose what warm ones hide.
7. **Exhaust the symptom, not the first cause.** Two independent root causes can hide behind one symptom. Finding a real bug doesn't close the investigation until the symptom is *fully* accounted for.
8. **Suspect your own code before the platform.** When a mature library misbehaves, re-read its public API before patching around it — a pile of band-aids on one subsystem usually marks a private-internals call that one public call replaces wholesale.

## The Band-Aid Counter

Count fixes applied to one symptom. **One** fix you can explain mechanistically: fine. **Two** fixes on the same symptom: the design is wrong — stop patching and rip the subsystem back to its root. That is almost always cheaper than the third patch.

## Exit Criteria

- Mechanism explained end-to-end, each link backed by an observation. This bar never scales down — a one-line fix needs its mechanism explained same as a rewrite.
- Original reproduction now passes; both sides of every branch you touched exercised.
- All instrumentation removed — zero residue.
- The fix is at the root. A fix downstream of the cause is a shim.

## Anti-Patterns — Stop and Retrace

- **Shotgun debugging** (change several things, rerun) — when it passes you've learned nothing; the bug retreats, it doesn't die.
- **Rerun-and-hope** — flakiness is a bug with a probability attached; reruns hide it until production rolls the dice.
- **Silencing the signal** (skip the test, ack the scanner, catch-and-pass) — you blinded the instrument, not the bug. Requires explicit permission, always.
- **Editing prod code to appease test infra** — fix the infra, not the product.
- **Blaming infrastructure first** — prove your application innocent with evidence before pointing outward.
- **Timeout/truncate as a "fix"** — silent data loss with a trigger condition; legitimate work can run for hours.
- **"It works now, moving on" / "probably harmless"** — if you can't say why it stopped, it hasn't.
- **Resetting credentials, state, or an environment to get past a failure** — that is destruction, not debugging. Stop and report.
