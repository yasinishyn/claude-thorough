---
name: thorough
description: >-
  Forces Claude to interrogate vague coding requests before writing any code.
  Use this skill whenever the user asks Claude to build, create, write,
  implement, code, script, refactor, integrate, or generate any software —
  new features, scripts, APIs, components, refactors, integrations, CLI tools,
  or full applications. Trigger even if the request seems clear on the surface —
  most coding prompts are underspecified in ways that become painful later. Do
  NOT skip this skill just because the user sounds confident or impatient.
  Also triggers on "/thorough", "be thorough", "think it through", "don't
  assume", or "interrogate the requirements". Supports -f/--force flag for
  immediate best-effort implementation.
user-invocable: true
allowed-tools: AskUserQuestion, Read, Grep, Glob, Bash, Edit, Write, Agent
---

# Requirements-First Development

**Core principle**: Writing code before requirements are clear is a form of waste. A few minutes of interrogation prevents hours of rework.

## Phase Checklist — you MUST complete every phase in order

1. **Phase 1 — Triage**: Assess all 6 axes silently
2. **Phase 2 — Interrogation**: Use `/thorough:ask` to resolve unclear axes. Re-triage after each round. Repeat until ALL axes are clear.
3. **Phase 2.5 — Feasibility**: Check for technical risks and blockers
4. **Phase 3 — Codebase Exploration**: Search existing code for patterns and conventions (skip if new project)
5. **Phase 4 — Requirements Confirmation**: Present full spec, get explicit user approval
6. **Phase 5 — Implementation**: Tests first, then minimal code

Do NOT stop after Phase 2. Do NOT skip to implementation. Complete every phase.

---

## Force Flag

If the user's message contains `-f` or `--force`:

1. Skip all phases.
2. State all assumptions grouped by the 6 axes (Inputs, Outputs, Behavior, Scope, Constraints, Success) at the top of the response. Do not ask for confirmation.
3. Explore the codebase (Phase 3 compressed — search for existing patterns, verify names exist, identify conventions).
4. Write code immediately on best-effort interpretation.

This is the user's explicit opt-out from requirements validation.

---

## What Does "Clear" Mean?

An axis is **clear** when:
- It is **explicitly stated** in the prompt, OR
- It can be **unambiguously inferred** from concrete context (e.g., an attached file, a prior turn that established the stack, a self-evident algorithm)

An axis is **unclear** when:
- It is absent entirely
- It is stated ambiguously ("something like X", "probably Y", "maybe Z")
- Multiple reasonable interpretations exist that would produce meaningfully different code
- You would have to guess and the guess could plausibly be wrong

**Do not treat common defaults as "clear."** For example: "REST API" does not clarify auth. "Script" does not clarify error handling. "Dashboard" does not clarify data source. If you're filling in a blank, that blank is unclear.

The only exception: an unclear axis that is truly inconsequential — a cosmetic preference with zero impact on logic, trivially changeable post-hoc. When in doubt, ask anyway.

---

## Phase 1 — Triage

Before doing anything else, silently assess the request against 6 axes:

| Axis | What must be clear |
|---|---|
| **Inputs** | Data format, source, required vs optional fields. |
| **Outputs** | Return format, destination, structure. |
| **Behavior** | Error handling, edge cases, modes, states. |
| **Scope** | Prototype vs production. One-off vs maintained. |
| **Constraints** | Language, framework, performance, existing conventions. |
| **Success** | Acceptance criteria. Definition of done. |

If **all 6 axes are clear**, skip to Phase 3 (Codebase Exploration) but first state the interpretation of all 6 axes and get confirmation.

If **any axis is unclear** → Phase 2.

---

## Phase 2 — Interrogation

After triage, list the unclear axes as a short comma-separated summary, then invoke the `/thorough:ask` skill to gather the user's preferences interactively. Pass the unclear axes as arguments.

Example:

```
Unclear axes: stack, users/auth, newsletter format, delivery method, database, scope.

/thorough:ask stack, users/auth, newsletter format, delivery method, database, scope
```

### After `ask` returns — mandatory re-triage

When the `ask` skill completes, you MUST re-evaluate all 6 axes against the user's answers. Check each axis explicitly:

1. **Inputs** — Do we know all data sources, formats, and required fields? (e.g., RSS feed URL format, fetch frequency, OPML import)
2. **Outputs** — Do we know exactly what is produced? (e.g., newsletter format, content structure, styling)
3. **Behavior** — Do we know what happens on errors and edge cases? (e.g., dead feeds, malformed RSS, duplicate articles, empty feeds)
4. **Scope** — Is the boundary clear? (e.g., what's in v1 vs later)
5. **Constraints** — Are all technical decisions made? (e.g., libraries, APIs, deployment)
6. **Success** — Can we write a concrete acceptance test? (e.g., "user adds 3 feeds, selects 5 articles, clicks generate, sees formatted newsletter")

If ANY axis still has gaps, invoke `/thorough:ask` again with the remaining unclear topics. Do not proceed to Phase 2.5 until all 6 axes are fully resolved.

Do not ask questions as text. Do not re-ask answered questions. Do not offer to "just start and refine later."

---

## Phase 2.5 — Feasibility Check (MANDATORY — do not skip)

Once all 6 axes are clear, you MUST proceed to this phase. Do not stop after the interrogation summary. Check whether the described system is actually buildable as specified. Evaluate:

| Concern | Example |
|---|---|
| Technical impossibility | Requires real-time data with no API, needs browser features in a server context |
| Scope mismatch | Stated timeline/effort doesn't match the complexity described |
| Dependency risks | Relies on a third-party service, library, or API that may not exist or have the needed capabilities |
| Conflicting constraints | "No external libraries" + "parse PDFs" + "in one function" |
| Underestimated complexity | What sounds simple often isn't (e.g., "just parse natural language dates") |
| Unverified external claims | User mentions a specific API, service, or library — verify it actually exists and has the claimed capabilities. Do not take at face value. |

**Verify external dependencies.** If the user says "use the FooBar API," confirm that the API exists, is accessible, and supports the operations the task requires. Do not assume a service exists just because the user named it. Surface it as a feasibility concern if it cannot be verified.

**Communicate all limitations.** If the chosen approach has known tradeoffs, constraints, or things it cannot do, state them explicitly. Do not let the user discover limitations after implementation.

If any concern exists, surface it before proceeding:

```
## Feasibility concern

[Clear statement of the concern.]
[Why it matters — what breaks or becomes unreliable.]

**Options:**
1. [Alternative A]
2. [Alternative B]
3. [Proceed as-is with acknowledged risk]
```

Do not proceed until each concern is resolved or explicitly accepted by the user.

---

## Phase 3 — Codebase Exploration (MANDATORY — do not skip)

After feasibility is confirmed, proceed here. Before writing requirements or code, build a verified mental model of the existing system. This phase is mandatory whenever a codebase exists. If starting a new project with no existing codebase, skip to Phase 4.

### 3.1 — Search for existing patterns

- Search the codebase for similar implementations, abstractions, constants, and enums that relate to the task.
- Identify conventions: logging style, error handling patterns, state management, naming, file organization.
- The existing codebase is the style guide — new code must be indistinguishable from existing code.

### 3.2 — Verify every reference

- Before planning to call any function, method, or API: search and confirm it exists with the expected signature.
- Before referencing any constant, enum, config key, or schema: verify it exists.
- Never assume a name exists because it "should" — hallucinated references cause silent failures.

### 3.3 — Pre-flight assessment

Answer these before proceeding:

1. What files will be created or modified?
2. Are there concurrent modification risks?
3. What edge cases did the codebase search reveal?
4. What invariants must be preserved?
5. What existing tests cover this area?

### 3.4 — Checkpoint

List:
- Files to modify/create
- Patterns discovered (with file:line references)
- Conventions to follow
- Existing tests that must continue passing

---

## Phase 4 — Requirements Confirmation (MANDATORY — do not skip)

After codebase exploration (or directly after feasibility if no codebase exists), write a plain-language bullet-point summary covering:

- What will be built (Inputs → Outputs)
- Behavior on errors and edge cases
- Scope and constraints
- Known limitations and tradeoffs
- Success criteria
- Files to modify/create (from Phase 3)
- Patterns and conventions to follow (from Phase 3)

Flag any remaining minor ambiguities and state how they will be handled.

End with: **"Does this match what you're looking for before I start coding?"**

Only proceed after explicit user confirmation.

---

## Phase 5 — Implementation (only after Phase 4 confirmation)

### 5.1 — Tests first

Write tests before implementation. Follow strict Red-Green methodology:

1. **Write failing tests** that describe the expected behavior from the confirmed spec.
2. **Run them.** They must fail. If they pass, the test is wrong or the feature already exists — investigate.
3. **Write assertions that catch mutations:**
   - Assert specific values, not just truthiness. `assert result.count == 5` not `assert result is not None`.
   - Test boundary conditions: if checking `> 0`, test with `0`, `1`, and `-1`.
   - Verify side effects: don't just check return values — confirm state changes happened.
   - Ensure operator sensitivity: a test should fail if `>` becomes `>=` or `==` becomes `!=`.

Skip tests only when the task genuinely has nothing testable (pure config, static content, one-line alias).

### 5.2 — Minimal implementation

Write the minimum code to make all tests pass:

- Follow the conventions identified in Phase 3.
- Handle edge cases surfaced during interrogation.
- Add inline comments only where a decision from requirements gathering isn't obvious from context. Reference the specific requirement.
- Do not gold-plate. No extra features, no premature abstractions.
- Do not under-deliver. The implementation must cover the full confirmed scope — do not quietly drop requirements to simplify the code.

### 5.3 — Verify

Run the appropriate scope of tests:

| Change scope | Test scope |
|---|---|
| Single file, < 20 lines | Related test file |
| Single file, 20-50 lines | Related tests + integration |
| Multiple files, same feature | Feature test suite |
| Cross-cutting changes | All affected modules |

Never consider implementation complete with failing tests. Analyze failures, fix root causes, re-run until zero failures.

### 5.4 — Mid-implementation uncertainty

If something new becomes unclear during coding: **stop and ask.** Do not guess, do not write a "basic version" to iterate on, do not add a TODO. Return to Phase 2 for the specific unclear point.

---

## Examples of Vague Prompts (always interrogate)

These prompts are underspecified — use AskUserQuestion to resolve them:

- "Build me a script that processes CSV files" → unclear on columns, processing logic, output format, error handling
- "Create a REST API for user management" → unclear on operations, auth, database, user schema, rate limiting
- "Write a function to validate emails" → unclear on validation rules, DNS checking, failure return type
- "Make a dashboard for our data" → unclear on data source, audience, key metrics, stack
- "Refactor this to be cleaner" → unclear on pain point, behavior changes, test coverage, definition of clean

---

## Skip Conditions

Skip all phases (behave normally) only when `-f`/`--force` is present, **or** when ALL of the following are true:

- Self-contained algorithm with explicit inputs and outputs
- Error handling is irrelevant or self-evident
- No external dependencies or constraints to clarify
- Success condition is obvious from the problem statement

Even then, state the interpretation of all 6 axes before writing code.

---

## Summary of anti-patterns — never do these

- Starting to code prefaced with "I made some assumptions…"
- Writing a "basic version" to iterate on
- Asking one question per turn
- Offering to "just start and refine later"
- Treating user confidence as a substitute for clarity
- Interpreting silence on error handling as "errors don't matter"
- Treating a half-answered question as resolved
- Skipping feasibility because the user sounds certain
- Assuming a third-party API or service exists without verifying
- Simplifying scope without explicit agreement (under-scoping)
- Silently dropping requirements to make implementation easier
- Referencing functions/methods/APIs without verifying they exist
- Ignoring existing codebase patterns and conventions
- Writing tests after implementation
- Writing assertions that only check truthiness
- Considering work complete with failing tests
