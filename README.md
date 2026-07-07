# ☕ Hold My Coffee

<p align="center">
  <img src="assets/hold-my-coffee.png" alt="A scowling senior dev in an «easily triggered» shirt holding out his coffee mug, captioned «Hold my Coffee»" width="820">
</p>

> [Agent Skills](https://docs.claude.com/en/docs/claude-code/skills) that make your coding agent work like a senior engineer, not a code generator — methodical, evidence-driven, and allergic to bad code.

## Table of Contents

- [What is this?](#what-is-this)
- [What's inside](#whats-inside)
- [Install](#install)
  - [Any agent (npx skills)](#any-agent-npx-skills)
  - [Claude Code (native plugin)](#claude-code-native-plugin)
  - [Update](#update)
  - [Uninstall](#uninstall)
- [How it works](#how-it-works)
- [About Dylan](#about-dylan)
- [Contributing](#contributing)
- [License](#license)

## What is this?

**Hold My Coffee** flips your agent from an eager junior into a methodical — and easily triggered — senior architect. It slows down to understand the system before it touches anything, ships the least code that does the job, has zero patience for code smell, and treats today's anti-pattern as next quarter's incident.

Underneath the attitude, that's a concrete set of instincts: demand evidence over agreement, fail loudly instead of swallowing errors, fix root causes rather than symptoms, treat every line of code as a tax, and only call something *done* once it's been demonstrated on the real thing.

Skills are an open, filesystem-based standard (a folder with a `SKILL.md`), so this pack works across **Claude Code, Codex, Cursor, and 70+ other agents** — one install command, no lock-in.

## What's inside

One skill, `sdlc`, which is a master map plus nine phase handbooks it pulls in on demand:

| | Handbook | Governs |
|---|---|---|
| 🧭 | `SKILL.md` | The stance, the 10 laws, the lifecycle, the drift protocol |
| 💡 | `brainstorming.md` | Exploring the problem before touching code |
| ✂️ | `task-breakdown.md` | Slicing work into vertical, shippable increments |
| 🔨 | `software-development.md` | Design, build, and test the real path |
| 🐞 | `debugging-techniques.md` | Root cause with an evidence chain |
| 🔍 | `technical-reviews.md` | Inspecting a change before it ships |
| ✅ | `qa-principles.md` | Proving the system in a fresh environment |
| 🎯 | `product-reviews.md` | Living the product through customer eyes |
| 📈 | `scalability-stability.md` | Cross-cutting: stability & security |
| 🎨 | `ui-ux-principles.md` | Cross-cutting: UI & UX |

The agent loads `SKILL.md` at the start of any software work and reads a given handbook only when it enters that phase.

## Install

### Any agent (npx skills)

The [`skills`](https://github.com/vercel-labs/skills) CLI installs into whatever agent you use — 70+ supported. Nothing to install; `npx` runs it. It auto-detects your agent and asks project-or-global:

```bash
npx skills add dylangrech92/hold-my-coffee
```

To target a specific agent, add `-a <slug>`. Add `-g` to install globally (every project instead of just this one):

```bash
npx skills add dylangrech92/hold-my-coffee -a claude-code       # Claude Code
npx skills add dylangrech92/hold-my-coffee -a codex             # Codex
npx skills add dylangrech92/hold-my-coffee -a cursor            # Cursor
npx skills add dylangrech92/hold-my-coffee -a pi                # Pi Agent
npx skills add dylangrech92/hold-my-coffee -a opencode          # OpenCode
npx skills add dylangrech92/hold-my-coffee -a gemini-cli        # Gemini CLI
npx skills add dylangrech92/hold-my-coffee -a antigravity-cli   # Antigravity CLI
npx skills add dylangrech92/hold-my-coffee -a hermes-agent      # Hermes Agent
npx skills add dylangrech92/hold-my-coffee -a openclaw          # OpenClaw
npx skills add dylangrech92/hold-my-coffee -a devin             # Devin CLI
```

> Install to several at once: `-a claude-code cursor codex`. Install everywhere: `-a '*'`. See the full agent list with `npx skills add --help`.

### Claude Code (native plugin)

Prefer Claude Code's built-in plugin marketplace? This repo is one:

```text
/plugin marketplace add dylangrech92/hold-my-coffee
/plugin install sdlc@hold-my-coffee
/reload-plugins
```

The skill is then namespaced as `sdlc:sdlc`.

### Update

```bash
npx skills update sdlc          # add -g if you installed globally
```

In Claude Code, refresh the marketplace instead: `/plugin marketplace update hold-my-coffee`.

### Uninstall

`npx skills remove sdlc` removes it from the agents where it's installed. Scope it exactly like install — same `-a <slug>` and `-g` flags:

```bash
npx skills remove sdlc                    # remove from the current project
npx skills remove sdlc -g                 # remove a global install
npx skills remove sdlc -a claude-code     # remove from one agent
npx skills remove sdlc -a '*'             # remove from every agent
```

In Claude Code, if you installed the native plugin: `/plugin uninstall sdlc@hold-my-coffee` (and `/plugin marketplace remove hold-my-coffee` to drop the marketplace).

## How it works

`SKILL.md` is deliberately short — the stance, ten laws, and a lifecycle table. Each row of that table points to one of the handbooks that sit alongside it. The agent doesn't front-load all of it; it reads the one handbook relevant to the phase it's in (brainstorming, breaking down, building, debugging, reviewing, QA, product review). Two handbooks — stability/security and UI/UX — cut across every phase.

The through-line of every rule: **keep the real state of the system visible.** Most engineering failures are a shortcut that hid something — a silent catch, a hollow wrapper, a scope creep, a band-aid on a wrong design. The laws exist to stop that.

## About Dylan

Hi — I'm **Dylan Grech** ([@dylangrech92](https://github.com/dylangrech92)), a software engineer who spends most of his time getting AI coding agents to produce work worth shipping. This pack is the distillation of the standards I hold my own code to, turned into something an agent can follow.

It was **built and curated by [Fable](https://www.anthropic.com/) — an Anthropic AI model — working alongside me.** (Not an official Anthropic product; just two collaborators who care about the craft.)

## Contributing

Contributions are welcome **as issues, not pull requests.** Open an issue to suggest a change, report a problem, or propose a new handbook. See [CONTRIBUTORS.md](CONTRIBUTORS.md) for the why and the how.

## License

[MIT](LICENSE) © Dylan Grech
