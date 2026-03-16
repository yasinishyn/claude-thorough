---
name: thorough
description: >-
  Forces Claude to interrogate vague coding requests before writing any code.
  Use this skill whenever the user asks Claude to build, create, write,
  implement, code, script, refactor, integrate, or generate any software.
  Trigger even if the request seems clear on the surface. Also triggers on
  "/thorough", "be thorough", "think it through", "don't assume", or
  "interrogate the requirements". Supports -f/--force flag to skip all phases.
user-invocable: true
allowed-tools: AskUserQuestion, Read, Grep, Glob, Bash, Edit, Write, Agent
---

# Requirements-First Development

Complete these phases in order. Do NOT stop early. Do NOT skip phases.

## Phase 1 — Triage (silent)

Assess the request against 6 axes: Inputs, Outputs, Behavior, Scope, Constraints, Success. If any axis is unclear, go to Phase 2.

## Phase 2 — Interrogation

Call the `AskUserQuestion` tool with up to 4 questions (2-4 options each). Put recommended options first with "(Recommended)" in the label. Use short headers (max 12 chars).

After answers, re-check all 6 axes. If gaps remain, call `AskUserQuestion` again. Repeat until all axes are clear, then proceed to Phase 2.5.

## Phase 2.5 — Feasibility

Check for technical impossibility, scope mismatches, dependency risks, conflicting constraints. Surface any concerns with options. If none, proceed to Phase 3.

## Phase 3 — Codebase Exploration

Search existing code for patterns, conventions, related implementations. Verify every function/API/constant you plan to reference actually exists. List files to modify, patterns to follow, tests that must keep passing. If new project with no codebase, skip to Phase 4.

## Phase 4 — Requirements Confirmation

Present a bullet-point spec covering: what will be built, error handling, scope, constraints, limitations, success criteria. End with: **"Does this match what you're looking for before I start coding?"** Wait for explicit confirmation.

## Phase 5 — Implementation

Tests first (Red-Green). Then minimal code to pass tests. Run tests. Fix failures. If something becomes unclear, stop and use AskUserQuestion to resolve it.

---

## Force Flag

If `-f` or `--force` is present: skip all phases, state assumptions, write code immediately.

## Skip Conditions

Skip all phases only when ALL are true: self-contained algorithm, error handling is self-evident, no external dependencies, success is obvious. Even then, state interpretation of all 6 axes first.
