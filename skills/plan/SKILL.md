---
name: plan
description: >-
  Plans a specific feature or user story for implementation. Reads the project
  requirements document (REQUIREMENTS.md), identifies what needs to be built,
  clarifies any feature-specific details, and produces an implementation plan.
  Use after /thorough:sketch has created the requirements doc. Pass the
  feature name or user story as an argument.
user-invocable: true
allowed-tools: AskUserQuestion, Read, Grep, Glob, Bash, Edit, Write, Agent
argument-hint: "[feature name or user story to plan]"
---

# Feature Planning

Plan a specific feature for implementation, based on the project requirements.

## Step 1 — Load context

Read `REQUIREMENTS.md` in the project root. If it doesn't exist, tell the user to run `/thorough:sketch` first.

Read the argument to understand which feature or user story to plan.

## Step 2 — Feature analysis

From the requirements doc, extract everything relevant to this feature:
- Related user stories
- Affected data model entities
- Related pages/endpoints/commands
- Dependencies on other features
- Error cases specific to this feature

Output a summary of what this feature involves.

## Step 3 — Clarify feature-specific details

If the requirements doc leaves any feature-specific details ambiguous, use `AskUserQuestion` to resolve them. Only ask about what's genuinely unclear for THIS feature — don't re-ask project-level decisions.

## Step 4 — Codebase exploration

Search existing code for:
- Patterns and conventions to follow
- Related implementations to build on
- Functions/APIs/constants to reference (verify they exist)
- Tests that cover this area

If this is a new project with no code yet, skip this step.

## Step 5 — Implementation plan

Produce a plan covering:

- **What will be created or modified** — list of files with what each does
- **Implementation order** — ordered by dependency (what must exist before what)
- **Test plan** — what tests to write, what they verify
- **Edge cases** — specific to this feature
- **Acceptance criteria** — how we know this feature is done

End with: **"Ready to implement? Run `/thorough:code` to start."**

---

## Force Flag

If `-f` or `--force` is present: skip questions, produce the plan immediately with assumptions marked.
