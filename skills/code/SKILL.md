---
name: code
description: >-
  TDD implementation phase. Use after /thorough:plan has produced a feature
  plan. Follows strict Red-Green methodology: write failing tests first,
  then minimal code to pass them. Also triggers on "/thorough:code",
  "start coding", "implement the plan".
user-invocable: true
allowed-tools: AskUserQuestion, Read, Grep, Glob, Bash, Edit, Write, Agent
argument-hint: "[optional: specific feature or user story to implement]"
---

# TDD Implementation

This skill implements a feature using strict Test-Driven Development, based on the plan from `/thorough:plan`.

## Step 1 — Locate the plan

Search the conversation history for the feature plan from `/thorough:plan`. Also read `REQUIREMENTS.md` for project context. If no plan exists, tell the user to run `/thorough:plan [feature]` first.

## Step 2 — Implementation order

Break the plan into implementable units ordered by dependency:

1. Project setup (scaffold, dependencies, config)
2. Data model (schema, migrations, ORM setup)
3. Core business logic (one user story at a time)
4. API routes / server-side logic
5. UI components and pages
6. Integration between layers
7. Edge cases, error handling, empty states

Present this order and get confirmation before starting.

## Step 3 — Red-Green cycle (for each unit)

1. **Write failing tests** that describe the expected behavior from the spec.
2. **Run them.** They must fail. If they pass, investigate — the test is wrong or the feature already exists.
3. **Write minimal code** to make tests pass.
4. **Run tests again.** All must pass.
5. **Refactor** if needed, keeping tests green.

### Test quality rules

- Assert specific values, not just truthiness. `expect(result.count).toBe(5)` not `expect(result).toBeTruthy()`.
- Test boundary conditions: if checking `> 0`, test with `0`, `1`, and `-1`.
- Verify side effects: don't just check return values — confirm state changes.
- Ensure operator sensitivity: a test should fail if `>` becomes `>=`.

## Step 4 — Mid-implementation uncertainty

If something becomes unclear during coding: **stop and ask.** Use `AskUserQuestion` to resolve it. Do not guess, do not write a "basic version" to iterate on, do not add a TODO.

## Step 5 — Verification

After all units are implemented:

1. Run the full test suite.
2. Verify against the success criteria from the plan.
3. List any deviations from the plan and explain why.
4. Present a summary of what was built.

Never consider implementation complete with failing tests.
