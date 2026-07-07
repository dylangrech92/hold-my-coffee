---
name: ui-ux-principles
description: Use when building or changing anything a user sees or touches — views, components, states, styling, copy, client-side data flow, or frontend architecture.
---

# UI/UX Principles

The UI is where the system's honesty about its own state either survives or dies. Two commitments govern everything: **the interface never lies about system state**, and **the display never owns the truth.**

## Data Flow — the Render Doctrine

- **The authoritative source owns the data.** Push channels (real-time connections, events) carry *triggers*, never authoritative payloads: on one, re-read from the source (an API, a store, the domain model) or flip a transient visual flag. A copy that doesn't exist can't diverge from the truth.
- **Templates render; logic lives elsewhere.** Views/components hold markup and bindings. State, API calls, and business rules live in dedicated state and service layers. A component that loads its own data is a lint error in spirit.
- **The data's owner formats; the display shows.** Timestamps, numbers, locale — formatted once by the authority that owns the data (a backend, a domain or service layer). Parsing that data again at the view is a second implementation waiting to disagree.
- **No display-layer mirror of authoritative state.** Re-reading from the source is cheap; reconciliation bugs are not.

## State Honesty

- **Every view designs all four states: loading, empty, error, success.** Empty and error are the states new customers meet first — the front door, not edge cases.
- **Never a dead screen.** From first paint the user sees the truth — a holding screen with honest status until the system is actually ready. A blank page reads as "broken", and to the user, is.
- **Blocking operations block visibly.** If the system cannot proceed until work completes (a file still processing, a save in flight), the UI says so and prevents the conflicting action. Letting the user continue and silently producing wrong results is the worst UI bug there is.
- **Errors reach the user in their language** — what happened, what to do next, in product vocabulary. Never raw exception text, never internal jargon, never silence.
- **Feedback is immediate.** Every action acknowledges instantly; long work shows honest progress; destructive actions confirm; disabled controls explain why.

## Consistency & Craft

- **One pattern per problem.** The same interaction solved two ways is a bug in the design system. Reuse the established pattern or explicitly replace it everywhere — never fork it.
- **Vocabulary is fixed.** UI copy uses the product's exact established names. Every invented synonym is a translation the user pays for.
- **Theme discipline.** All colors and spacing through design tokens/theme variables — never hardcoded. Every change verified in both light and dark themes before it's done.
- **Hierarchy over decoration.** The most important element is visually primary; progressive disclosure over wall-of-controls. Imagery must demonstrate or inform — decorative noise dilutes the signal.
- **Accessible by default.** Every control reachable by keyboard, focus visible, contrast honest, imagery described in text. A UI that demands a mouse and sharp eyesight is broken for part of the audience — and that's just "broken" with a smaller blast radius.
- **Guard redirect/navigation logic like auth code** — it usually is. Guards comparing the wrong state (current vs target) produce infinite navigation loops and redundant work — redirect loops, request floods. Navigation gets the same review rigor as backend logic.

## Anti-Patterns

- **Authoritative data over a push channel** — display and source now disagree about the truth; every reconnect or missed event is a coin flip.
- **Optimistic UI without rollback** — showing a success that may not have happened is lying with extra steps.
- **Silent UI failure** — a failed action with no feedback teaches the user the product is haunted.
- **Spinner-forever** — an unbounded wait with no status is a dead screen in a costume.
- **Hardcoded colors/spacing** — breaks theming today, guarantees inconsistency forever.
- **Inventing UI names for existing concepts** — the docs, the UI, and the code now speak three languages.
