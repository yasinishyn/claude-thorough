---
name: sketch
description: >-
  Creates a full project requirements document before any code is written.
  Analyzes what kind of software is being built, generates project-specific
  requirement categories, and gathers details through interactive questions.
  Produces a structured requirements doc that serves as the source of truth
  for all subsequent feature planning and implementation. Use when starting
  a new project or when no requirements doc exists yet.
user-invocable: true
allowed-tools: AskUserQuestion, Read, Grep, Glob, Bash, Edit, Write, Agent
---

# Project Requirements Sketch

Create a comprehensive requirements document tailored to the specific project.

## Step 1 — Analyze the request

Read the user's description and determine:

- **Software type**: web app, mobile app, CLI, API/service, library/SDK, script/automation, data pipeline, desktop app, browser extension, or hybrid
- **Complexity level**: simple (single-purpose tool), medium (multi-feature app), complex (multi-user system with integrations)
- **Key domains involved**: what problem spaces does this touch?

Output a brief 1-2 sentence summary of what you understand so far.

## Step 2 — Generate requirement categories

Based on the software type and complexity, generate a list of requirement categories that are RELEVANT to this specific project. Do not use a fixed template.

**Always include these core categories:**
- Problem Statement — why this exists, what problem it solves
- Actors — who or what interacts with it (users, systems, services, cron jobs)
- Capabilities — what it does (described as user stories or use cases)
- Data — what data exists, structure, relationships, storage
- Technical Decisions — stack, dependencies, deployment, constraints
- Error Handling — what can go wrong, recovery strategies
- Scope — what's in v1, what's explicitly deferred, non-goals
- Success Criteria — concrete acceptance tests

**Add type-specific categories only when relevant. Examples:**
- Web/mobile: Pages & Navigation, UI/UX & Interactions, Responsive Behavior, Authentication & Authorization
- API/service: Endpoints, Request/Response Schemas, Auth Strategy, Rate Limiting, Versioning
- CLI: Commands & Subcommands, Flags & Options, Input/Output Formats, Help Text, Exit Codes
- Library/SDK: Public API Surface, Method Signatures, Type Definitions, Backward Compatibility
- Data pipeline: Sources, Transformations, Destinations, Scheduling, Monitoring
- Any multi-user: Roles & Permissions, User Flows per Role
- Any with external deps: Integration Points, API Dependencies, Fallback Behavior

**Do not add categories that don't apply.** A CLI tool does not need "Pages & Navigation." An API does not need "UI/UX."

Output the list of categories you will cover and why each is relevant.

## Step 3 — Interrogation

Use `AskUserQuestion` to gather information for each category. Group related categories into rounds of up to 4 questions.

**Prioritize rounds:**
1. Problem, actors, and scope (the "why" and "who")
2. Core capabilities and workflows (the "what")
3. Data and technical decisions (the "how")
4. Type-specific details (UI, API design, CLI structure, etc.)
5. Error handling, edge cases, and success criteria

After each round, assess what's still unclear. Generate follow-up questions specific to what the user's answers revealed. New answers often expose new categories — add them dynamically.

Continue until you have enough detail to write the requirements document. Not every detail needs to be pinned down — mark genuinely uncertain items as "TBD" rather than asking infinite questions.

## Step 4 — Feasibility check

Before writing the document, check for:
- Technical impossibility or conflicting requirements
- Dependencies on APIs/services that may not exist
- Scope that doesn't match the stated complexity level
- Underestimated complexity

Surface concerns and resolve them before proceeding.

## Step 5 — Write the requirements document

Produce a structured document with:

1. **A heading for each relevant category** from Step 2
2. **Concrete details** under each heading — not vague descriptions
3. **User stories** in "As a [actor], I want [action], so that [outcome]" format
4. **Data model** with entities, fields (with types), and relationships
5. **Scope boundary** — explicit "in v1" and "not in v1" lists
6. **Success criteria** as testable acceptance conditions

Format the document as markdown. Write it to a file called `REQUIREMENTS.md` in the project root.

End with: **"Requirements document created. Review it and let me know if anything needs changes. When you're ready to implement a feature, use `/thorough:plan [feature]`."**

---

## Force Flag

If `-f` or `--force` is present: skip questions, make best-effort assumptions, write the document immediately with assumptions clearly marked.
