# thorough

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that forces requirements interrogation before any code gets written. No more "I made some assumptions" — every request goes through structured clarification, feasibility checks, codebase exploration, and explicit confirmation before a single line is generated.

## Why

Claude is eager to help. Sometimes too eager. Given a vague prompt like "build me an API," it will pick a framework, invent a schema, guess at auth, and hand you 200 lines of code built on assumptions you never agreed to.

`thorough` fixes this by enforcing a 5-phase pipeline that treats ambiguity as a blocker, not a creative opportunity.

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

Asks every question needed to resolve ambiguity. Multiple rounds if necessary. Questions are batched and grouped by category — no drip-feeding one at a time. Contradictions are flagged immediately.

### Phase 2.5 — Feasibility Check

Before confirming the spec, checks for technical impossibility, scope mismatches, dependency risks, and conflicting constraints. Surfaces concerns with concrete alternatives.

### Phase 3 — Codebase Exploration

Searches the existing codebase for patterns, conventions, and related implementations. Verifies that every function, method, and API it plans to reference actually exists. Documents files to modify, conventions to follow, and existing tests that must keep passing.

### Phase 4 — Requirements Confirmation

Presents a plain-language bullet-point spec covering everything discovered in Phases 1-3. Waits for your explicit "yes, go ahead" before proceeding.

### Phase 5 — Implementation

Writes tests first with mutation-resistant assertions (specific values, boundary conditions, side-effect verification). Then writes the minimum code to pass those tests. If something becomes unclear mid-implementation, stops and asks rather than guessing.

## Install

### Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed and working

### Option A — Personal install (available in all your projects)

```bash
mkdir -p ~/.claude/skills/thorough
cp SKILL.md ~/.claude/skills/thorough/SKILL.md
```

### Option B — Project install (shared with collaborators via git)

```bash
mkdir -p .claude/skills/thorough
cp SKILL.md .claude/skills/thorough/SKILL.md
git add .claude/skills/thorough/SKILL.md
git commit -m "Add thorough skill"
```

### Option C — Clone and install

```bash
git clone <repo-url> /tmp/thorough
mkdir -p ~/.claude/skills/thorough
cp /tmp/thorough/SKILL.md ~/.claude/skills/thorough/SKILL.md
```

### Verify installation

Start a new Claude Code session and run:

```
/thorough hello
```

If installed correctly, Claude will triage the request and start asking questions instead of writing code.

## Usage

### Slash command

```
/thorough build an API endpoint for user registration
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
/thorough -f parse this CSV and dump it to JSON
```

This skips all phases, states assumptions upfront, and writes code immediately. It still does a quick codebase scan to respect existing conventions.

### Skip conditions

The skill automatically skips interrogation (but still states its interpretation) when all of these are true:

- Self-contained algorithm with explicit inputs and outputs
- Error handling is irrelevant or self-evident
- No external dependencies or constraints to clarify
- Success condition is obvious from the problem statement

## What it prevents

| Anti-pattern | How thorough handles it |
|---|---|
| "I made some assumptions…" | Asks instead of assuming |
| Writing a "basic version" to iterate on | Gets the spec right first |
| One question per turn | Batches all questions per round |
| Treating user confidence as clarity | Evaluates the 6 axes regardless |
| Silence on errors = errors don't matter | Explicitly asks about error handling |
| Half-answered question = resolved | Re-triages after every response |
| Hallucinated function references | Searches codebase to verify before using |
| Tests after code | Tests first, always |

## File structure

```
thorough/
├── SKILL.md    # The skill itself — all instructions, no code
└── README.md   # This file
```

## License

MIT
