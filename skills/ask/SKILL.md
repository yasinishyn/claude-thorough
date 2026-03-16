---
name: ask
description: >-
  Presents unclear requirements as interactive multiple-choice questions using
  the AskUserQuestion tool. Call this after identifying unclear axes during
  requirements triage. Pass unclear axes as arguments.
user-invocable: true
allowed-tools: AskUserQuestion
argument-hint: "[list of unclear topics to ask about]"
---

# Ask — Interactive Requirements Gathering

You have been given a list of unclear topics. Your job is to call the `AskUserQuestion` tool. That is your ONLY job.

## Instructions

1. Read the arguments passed to this skill. They describe what needs to be clarified.
2. Convert each unclear topic into a question with 2-4 concrete options.
3. Call `AskUserQuestion` with up to 4 questions.
4. After the user responds, check if any of the passed topics are still unclear. If yes, call `AskUserQuestion` again.
5. When all passed topics have answers, output ONLY the raw answers as a simple key: value list. Do NOT output a formatted summary table. Do NOT say "here's a summary" or treat this as a final deliverable. The calling skill will continue processing after you finish.

## Formatting rules

- Each question needs: `question` (string), `header` (max 12 chars), `options` (2-4 items), `multiSelect` (boolean)
- Each option needs: `label` (concise choice), `description` (what it means for implementation)
- Put the recommended option first with "(Recommended)" in the label
- Use `multiSelect: true` only when choices are not mutually exclusive

## What NOT to do

- Do NOT write questions as text output
- Do NOT output bullet points with question marks
- Do NOT skip calling the tool
- Your ONLY output before the tool call should be one short sentence like "Let me gather your preferences."
