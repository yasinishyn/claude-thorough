# thorough

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) plugin for requirements-first development. Separates project requirements, feature planning, and implementation into distinct phases — so Claude asks the right questions before writing any code.

Questions are presented as **interactive multiple-choice selections** — no walls of text to read and respond to.

## Why

Claude is eager to help. Sometimes too eager. Given a vague prompt like "build me an API," it will pick a framework, invent a schema, guess at auth, and hand you 200 lines of code built on assumptions you never agreed to.

`thorough` fixes this with three commands:

| Command | Purpose |
|---|---|
| **`/thorough:sketch`** | Create a full project requirements document |
| **`/thorough:plan`** | Plan a specific feature for implementation |
| **`/thorough:code`** | TDD implementation of a planned feature |

## How it works

### `/thorough:sketch` — Project requirements

Creates a comprehensive requirements document (`REQUIREMENTS.md`) tailored to your specific project. Unlike fixed templates, it:

1. **Analyzes your request** to determine what kind of software you're building (web app, CLI, API, library, etc.)
2. **Generates project-specific categories** — a web app gets Pages & Navigation; a CLI gets Commands & Flags; an API gets Endpoints & Auth. No irrelevant sections.
3. **Asks targeted questions** through interactive multiple-choice, in priority rounds
4. **Checks feasibility** before committing to the spec
5. **Writes a structured requirements document** with user stories, data model, scope boundaries, and acceptance tests

### `/thorough:plan` — Feature planning

Plans a specific feature or user story from the requirements document:

1. **Reads `REQUIREMENTS.md`** and extracts everything relevant to the feature
2. **Clarifies feature-specific details** that the requirements doc left open
3. **Explores the codebase** for patterns, conventions, and related implementations
4. **Produces an implementation plan** with file list, build order, test plan, and edge cases

### `/thorough:code` — TDD implementation

Implements the planned feature using strict Test-Driven Development:

1. Breaks the plan into implementable units ordered by dependency
2. For each unit: write failing tests → run → write minimal code → run → refactor
3. Verifies against acceptance criteria from the plan
4. Stops and asks if anything becomes unclear mid-implementation

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
mkdir -p ~/.claude/skills/sketch ~/.claude/skills/plan ~/.claude/skills/code
cp /tmp/thorough/skills/sketch/SKILL.md ~/.claude/skills/sketch/SKILL.md
cp /tmp/thorough/skills/plan/SKILL.md ~/.claude/skills/plan/SKILL.md
cp /tmp/thorough/skills/code/SKILL.md ~/.claude/skills/code/SKILL.md
```

With this method skills are invoked as `/sketch`, `/plan`, `/code` (no namespace prefix).

### Option C — Project install (shared with collaborators via git)

```bash
git clone https://github.com/yasinishyn/claude-thorough.git /tmp/thorough
mkdir -p .claude/skills/sketch .claude/skills/plan .claude/skills/code
cp /tmp/thorough/skills/sketch/SKILL.md .claude/skills/sketch/SKILL.md
cp /tmp/thorough/skills/plan/SKILL.md .claude/skills/plan/SKILL.md
cp /tmp/thorough/skills/code/SKILL.md .claude/skills/code/SKILL.md
git add .claude/skills/
git commit -m "Add thorough skills"
```

### Verify installation

Start a new Claude Code session and run:

```
/thorough:sketch build me a website
```

If installed correctly, Claude will analyze your request and present interactive questions instead of writing code.

## Usage

### Typical workflow

```
# Step 1: Define the project
/thorough:sketch build me a website where I can add RSS feeds and it generates a newsletter

# (Answer interactive questions across multiple rounds)
# (Review REQUIREMENTS.md — edit if needed)

# Step 2: Plan a feature
/thorough:plan add RSS feed management

# (Review the implementation plan)

# Step 3: Implement
/thorough:code

# Step 4: Plan the next feature
/thorough:plan generate newsletter from articles

# ...and so on
```

### Force flag (skip all phases)

```
/thorough:sketch -f parse this CSV and dump it to JSON
```

Skips all phases, states assumptions upfront, and writes the document immediately.

### Natural language triggers

These phrases also activate `/thorough:sketch`:

- "be thorough"
- "think it through"
- "don't assume"
- "plan this out"
- "interrogate the requirements"

## File structure

```
claude-thorough/
├── .claude-plugin/
│   ├── plugin.json          # Plugin manifest
│   └── marketplace.json     # Marketplace manifest for distribution
├── skills/
│   ├── sketch/
│   │   └── SKILL.md         # Project requirements document creation
│   ├── plan/
│   │   └── SKILL.md         # Feature planning
│   ├── code/
│   │   └── SKILL.md         # TDD implementation
│   └── ask/
│       └── SKILL.md         # Internal: interactive questioning tool
├── README.md
└── LICENSE
```

## License

MIT
