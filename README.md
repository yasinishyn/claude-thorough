# thorough

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) plugin for requirements-first development. No more "I made some assumptions" — every request goes through deep requirements gathering with user stories, user flows, data modeling, and explicit confirmation before a single line is generated.

Questions are presented as **interactive multiple-choice selections** — no walls of text to read and respond to.

## Why

Claude is eager to help. Sometimes too eager. Given a vague prompt like "build me an API," it will pick a framework, invent a schema, guess at auth, and hand you 200 lines of code built on assumptions you never agreed to.

`thorough` fixes this with two skills that separate **planning** from **coding**:

- **`/thorough:plan`** — Deep requirements gathering across 12 axes: user roles, user stories, user flows, data model, UI/UX, inputs, outputs, behavior, scope, constraints, integrations, and success criteria. Produces a structured requirements document.
- **`/thorough:code`** — TDD implementation of the approved plan. Strict Red-Green methodology: failing tests first, then minimal code.

## How it works

### `/thorough:plan` — 5 phases

#### Phase 1 — Triage
Silently evaluates your request against 12 axes (not just the usual 6). Covers user roles, user stories, user flows, data model, UI/UX, and more.

#### Phase 2 — Interrogation
Presents interactive multiple-choice questions in priority rounds:
1. **Who and What** — roles, core user stories, MVP scope
2. **How It Works** — user flows, data model, UI/UX
3. **Technical Decisions** — stack, database, integrations, deployment
4. **Edge Cases** — errors, empty states, validation, success criteria

Re-triages after every round until all 12 axes are clear.

#### Phase 3 — Feasibility
Checks for technical blockers, scope mismatches, dependency risks. Verifies external APIs actually exist.

#### Phase 4 — Codebase Exploration
Searches existing code for patterns and conventions. Skipped for new projects.

#### Phase 5 — Requirements Document
Structured spec with: user roles, user stories, user flows, data model, pages/screens, technical spec, scope boundaries, and acceptance tests. Waits for explicit approval before any code is written.

### `/thorough:code` — TDD implementation

After the plan is approved:
1. Breaks the plan into implementable units ordered by dependency
2. For each unit: write failing tests → run → write minimal code → run → refactor
3. Verifies against success criteria from the plan
4. Stops and asks if anything becomes unclear mid-implementation

### `/thorough:ask` — Standalone interactive questions

Use directly to gather preferences on specific topics:
```
/thorough:ask stack, database, auth method, deployment
```

## Install

### Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed and working

### Option A — Plugin install (recommended)

```
/plugin marketplace add yasinishyn/claude-thorough
/plugin install thorough@yasinishyn-claude-thorough
/reload-plugins
```

### Option B — Personal install (available in all your projects)

```bash
git clone https://github.com/yasinishyn/claude-thorough.git /tmp/thorough
mkdir -p ~/.claude/skills/plan ~/.claude/skills/code ~/.claude/skills/ask
cp /tmp/thorough/skills/plan/SKILL.md ~/.claude/skills/plan/SKILL.md
cp /tmp/thorough/skills/code/SKILL.md ~/.claude/skills/code/SKILL.md
cp /tmp/thorough/skills/ask/SKILL.md ~/.claude/skills/ask/SKILL.md
```

With this method skills are invoked as `/plan`, `/code`, `/ask` (no namespace prefix).

### Option C — Project install (shared with collaborators via git)

```bash
git clone https://github.com/yasinishyn/claude-thorough.git /tmp/thorough
mkdir -p .claude/skills/plan .claude/skills/code .claude/skills/ask
cp /tmp/thorough/skills/plan/SKILL.md .claude/skills/plan/SKILL.md
cp /tmp/thorough/skills/code/SKILL.md .claude/skills/code/SKILL.md
cp /tmp/thorough/skills/ask/SKILL.md .claude/skills/ask/SKILL.md
git add .claude/skills/
git commit -m "Add thorough skills"
```

### Verify installation

Start a new Claude Code session and run:

```
/thorough:plan build me a website
```

If installed correctly, Claude will triage the request and present interactive questions instead of writing code.

## Usage

### Typical workflow

```
# Step 1: Plan
/thorough:plan build me a website where I can add RSS feeds and it generates a newsletter

# (Answer interactive questions across multiple rounds)
# (Review and approve the requirements document)

# Step 2: Implement
/thorough:code
```

### Force flag (skip all phases)

```
/thorough:plan -f parse this CSV and dump it to JSON
```

Skips all phases, states assumptions upfront, and writes code immediately.

### Natural language triggers

These phrases also activate `/thorough:plan`:

- "be thorough"
- "think it through"
- "don't assume"
- "plan this out"
- "interrogate the requirements"

## What it asks that others don't

| Axis | Example questions |
|---|---|
| **User Roles** | Who uses this? Admin vs end user? Permissions? |
| **User Stories** | As a [role], I want [action], so that [outcome] |
| **User Flows** | Step-by-step: what does the user see, click, experience? |
| **Data Model** | What entities? Relationships? Required fields? |
| **UI/UX** | What pages? Navigation? Responsive? Design style? |
| **Error Handling** | Dead feeds? Malformed data? Network failures? |
| **Empty States** | What does the user see when there's no data yet? |
| **Scope** | What's in v1? What's explicitly out? |

## File structure

```
claude-thorough/
├── .claude-plugin/
│   ├── plugin.json          # Plugin manifest
│   └── marketplace.json     # Marketplace manifest for distribution
├── skills/
│   ├── plan/
│   │   └── SKILL.md         # Requirements gathering — 5 phases
│   ├── code/
│   │   └── SKILL.md         # TDD implementation
│   └── ask/
│       └── SKILL.md         # Standalone interactive questioning
├── README.md
└── LICENSE
```

## License

MIT
