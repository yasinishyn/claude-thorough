# thorough

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) plugin that forces requirements interrogation before any code gets written. No more "I made some assumptions" — every request goes through structured clarification, feasibility checks, codebase exploration, and explicit confirmation before a single line is generated.

Questions are presented as **interactive multiple-choice selections** using Claude Code's AskUserQuestion tool — no more walls of text to read and respond to.

## Why

Claude is eager to help. Sometimes too eager. Given a vague prompt like "build me an API," it will pick a framework, invent a schema, guess at auth, and hand you 200 lines of code built on assumptions you never agreed to.

`thorough` fixes this by enforcing a 6-phase pipeline that treats ambiguity as a blocker, not a creative opportunity.

## How it works

### Phase 1 — Triage

Silently evaluates your request against 6 axes:

| Axis | What must be clear |
|---|---|
| **Inputs** | What data comes in? Format, source, required vs optional. |
| **Outputs** | What is returned/produced/emitted? Format, destination. |
| **Behavior** | What happens on errors and edge cases? Modes or states? |
| **Scope** | Prototype or production? One-off or maintained? |
| **Constraints** | Language, framework, performance, existing codebase conventions. |
| **Success** | How will you know it works? What does "done" look like? |

If anything is unclear, it moves to Phase 2 instead of guessing.

### Phase 2 — Interrogation

Presents interactive multiple-choice questions using Claude Code's `AskUserQuestion` tool. You click to select answers instead of typing paragraphs. Multiple rounds if necessary — re-triages after every response until all 6 axes are clear.

### Phase 3 — Feasibility

Checks for technical impossibility, scope mismatches, dependency risks, and conflicting constraints. Surfaces concerns with concrete alternatives before proceeding.

### Phase 4 — Codebase Exploration

Searches the existing codebase for patterns, conventions, and related implementations. Verifies that every function, method, and API it plans to reference actually exists. Documents files to modify, conventions to follow, and existing tests that must keep passing. Skipped for new projects with no existing codebase.

### Phase 5 — Requirements Confirmation

Presents a plain-language bullet-point spec covering everything discovered in Phases 1-4. Waits for your explicit "yes, go ahead" before proceeding.

### Phase 6 — Implementation

Writes tests first with mutation-resistant assertions (specific values, boundary conditions, side-effect verification). Then writes the minimum code to pass those tests. If something becomes unclear mid-implementation, stops and asks rather than guessing.

## Install

### Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed and working

### Option A — Plugin install (recommended)

Add the marketplace and install the plugin from within Claude Code:

```
/plugin marketplace add yasinishyn/claude-thorough
/plugin install thorough@yasinishyn-claude-thorough
/reload-plugins
```

The skill is invoked as `/thorough:thorough`. The plugin also includes `/thorough:ask` for standalone interactive questioning.

### Option B — Personal install (available in all your projects)

```bash
git clone https://github.com/yasinishyn/claude-thorough.git /tmp/thorough
mkdir -p ~/.claude/skills/thorough
cp /tmp/thorough/skills/thorough/SKILL.md ~/.claude/skills/thorough/SKILL.md
```

With this method the skill is invoked as `/thorough` (no namespace prefix).

### Option C — Project install (shared with collaborators via git)

```bash
git clone https://github.com/yasinishyn/claude-thorough.git /tmp/thorough
mkdir -p .claude/skills/thorough
cp /tmp/thorough/skills/thorough/SKILL.md .claude/skills/thorough/SKILL.md
git add .claude/skills/thorough/SKILL.md
git commit -m "Add thorough skill"
```

### Verify installation

Start a new Claude Code session and run:

```
/thorough:thorough hello
```

If installed correctly, Claude will triage the request and present interactive questions instead of writing code.

## Usage

### Slash command

```
/thorough:thorough build an API endpoint for user registration
```

### Natural language triggers

These phrases also activate the skill:

- "be thorough"
- "think it through"
- "don't assume"
- "interrogate the requirements"

Example: _"Don't assume anything — I need a caching layer for our API."_

### Force flag (skip all phases)

When you know exactly what you want and don't need interrogation, add `-f` or `--force`:

```
/thorough:thorough -f parse this CSV and dump it to JSON
```

This skips all phases, states assumptions upfront, and writes code immediately.

### Standalone interactive questions

Use the `ask` skill directly to gather preferences on specific topics:

```
/thorough:ask stack, database, auth method, deployment
```

### Skip conditions

The skill automatically skips interrogation (but still states its interpretation) when all of these are true:

- Self-contained algorithm with explicit inputs and outputs
- Error handling is irrelevant or self-evident
- No external dependencies or constraints to clarify
- Success condition is obvious from the problem statement

## What it prevents

| Anti-pattern | How thorough handles it |
|---|---|
| "I made some assumptions..." | Asks instead of assuming |
| Writing a "basic version" to iterate on | Gets the spec right first |
| One question per turn | Batches questions as interactive selections |
| Treating user confidence as clarity | Evaluates the 6 axes regardless |
| Silence on errors = errors don't matter | Explicitly asks about error handling |
| Half-answered question = resolved | Re-triages after every response |
| Hallucinated function references | Searches codebase to verify before using |
| Tests after code | Tests first, always |

## File structure

```
claude-thorough/
├── .claude-plugin/
│   ├── plugin.json          # Plugin manifest
│   └── marketplace.json     # Marketplace manifest for distribution
├── skills/
│   ├── thorough/
│   │   └── SKILL.md         # Main skill — 6-phase pipeline
│   └── ask/
│       └── SKILL.md         # Standalone interactive questioning
├── README.md
└── LICENSE
```

## License

MIT
