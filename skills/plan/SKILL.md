---
name: plan
description: >-
  Deep requirements gathering before writing any code. Covers user roles,
  user stories, user flows, data model, UI/UX, error handling, and technical
  constraints. Use this skill whenever the user asks to build, create, write,
  implement, or generate any software. Also triggers on "/thorough", "be
  thorough", "think it through", "don't assume", or "plan this out".
  Supports -f/--force flag to skip all phases.
user-invocable: true
allowed-tools: AskUserQuestion, Read, Grep, Glob, Bash, Edit, Write, Agent
---

# Requirements-First Development

Complete these phases in order. Do NOT stop early. Do NOT skip phases.

## Phase 1 — Triage (silent)

Assess the request against these axes. If any axis is unclear, go to Phase 2.

| Axis | What must be clear |
|---|---|
| **Users & Roles** | Who uses this? What roles exist? What can each role do? |
| **User Stories** | As a [role], I want [action], so that [outcome] — for every key feature |
| **User Flows** | Step-by-step: what does the user see, click, and experience? |
| **Data Model** | What entities exist? What are their relationships? What fields? |
| **UI/UX** | What pages/screens? Navigation? Key interactions? Responsive? |
| **Inputs** | What data comes in? Format, source, validation rules? |
| **Outputs** | What is produced? Format, destination, structure? |
| **Behavior** | Error handling, edge cases, loading states, empty states? |
| **Scope** | MVP vs full product? What's in v1 vs later? |
| **Constraints** | Stack, framework, performance, accessibility, existing conventions? |
| **Integration** | External APIs, services, auth providers, email, payments? |
| **Success** | Acceptance criteria. How do we know it works? |

## Phase 2 — Interrogation

Call the `AskUserQuestion` tool to resolve unclear axes. Up to 4 questions per call, 2-4 options each. Put recommended options first with "(Recommended)" in the label. Use short headers (max 12 chars).

### Question rounds by priority

**Round 1 — Who and What**
- User roles and permissions
- Core user stories (the 3-5 most important things users do)
- Scope (MVP boundaries)

**Round 2 — How It Works**
- Key user flows (step by step for the main 2-3 scenarios)
- Data model (main entities and relationships)
- UI/UX approach (pages, navigation, design style)

**Round 3 — Technical Decisions**
- Stack and framework
- Database and storage
- External integrations (APIs, auth, email, etc.)
- Deployment target

**Round 4 — Edge Cases and Polish**
- Error handling strategy
- Empty states and loading states
- Validation rules
- Success criteria and acceptance tests

After each round, re-check all axes. If gaps remain, call `AskUserQuestion` again. Do NOT proceed until every axis is resolved.

## Phase 3 — Feasibility

Check for technical impossibility, scope mismatches, dependency risks, conflicting constraints. Verify that external APIs/services mentioned actually exist and support the needed operations. Surface any concerns with options. If none, proceed to Phase 4.

## Phase 4 — Codebase Exploration

Search existing code for patterns, conventions, related implementations. Verify every function/API/constant you plan to reference actually exists. List files to modify, patterns to follow, tests that must keep passing. If new project with no codebase, skip to Phase 5.

## Phase 5 — Requirements Document

Present a structured requirements document:

### 5.1 — User Roles
List each role and their permissions/capabilities.

### 5.2 — User Stories
List as: "As a [role], I want [action], so that [outcome]" — cover every feature.

### 5.3 — User Flows
For each key flow, list the step-by-step sequence: what the user sees, clicks, and what happens.

### 5.4 — Data Model
List entities, their fields (with types), and relationships.

### 5.5 — Pages/Screens
List each page, what it shows, and key interactions.

### 5.6 — Technical Spec
Stack, database, integrations, deployment, error handling strategy.

### 5.7 — Scope
What's in v1. What's explicitly out of scope.

### 5.8 — Success Criteria
Concrete acceptance tests — "when I do X, I should see Y."

End with: **"Does this match what you're looking for? Say 'approved' to proceed to implementation with `/thorough:code`."**

Wait for explicit confirmation. Do NOT write any code.

---

## Force Flag

If `-f` or `--force` is present: skip all phases, state assumptions, write code immediately.

## Skip Conditions

Skip all phases only when ALL are true: self-contained algorithm, error handling is self-evident, no external dependencies, success is obvious. Even then, state interpretation of all axes first.
