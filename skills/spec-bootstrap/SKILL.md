---
name: spec-bootstrap
description: Use when starting a new project or new capability area before any code is written, to create the initial specs/ structure.
---

# Spec: Bootstrap

## Overview

Bootstrap creates the spec foundation for a project or new capability area. It does not write anything until the input clears the quality bar. A vague brief produces bad specs; bad specs undermine the rebuild guarantee.

## Before Writing Anything

Evaluate the input against the quality bar. Four conditions must all be met before a single spec file is created:

1. **Core purpose is unambiguous** — the problem being solved and who it's for are clear enough that two developers would build the same thing
2. **Tech stack is known or decidable** — language, runtime, key libraries, storage; if none specified, enough context exists to make a defensible choice with rationale
3. **Non-scope is defined** — what this project explicitly does not do (prevents scope creep in specs)
4. **At least one capability area** can be identified with its decisions and invariants

If any condition is unmet, ask before writing. "Well-understood domain" does not substitute for unambiguous input — a task manager could be a CLI tool, a web app, a mobile app, or a Slack bot. Assume nothing.

## Clarification Protocol

Ask targeted questions — **one per gap, in priority order** — before writing anything:

1. Core purpose gap → "What problem does this solve, and who uses it?"
2. Platform/interface gap → "How do users interact with it — CLI, web, mobile, API, other?"
3. Tech stack gap → "Any constraints on language, framework, or infrastructure?"
4. Non-scope gap → "What should this explicitly never do?"
5. Capability gap → "What are the main functional areas?"

Stop after the first unanswered question. Wait for the response before asking the next. Do not front-load all questions at once.

**Do not write partial specs while waiting.** Partial specs are worse than no specs.

## The Wall

If the description is too vague to identify core purpose and platform after one clarification round, state this explicitly:

> "I don't have enough to write specs that would clear the quality bar. I need at minimum: what this does, who uses it, and how they interact with it. Can you give me a one-paragraph description?"

Do not attempt to infer. Do not write placeholder specs. Do not proceed.

## Output (when quality bar is met)

Create these files, in this order:

### 1. `specs/project.md`

```markdown
# Project

## Purpose
What this project does and why it exists.

## Non-scope
What this project explicitly does not do.

## Tech stack
Key technology choices with rationale. Rejected alternatives where relevant.

## Architecture
The structural approach and why. Not a diagram — a decision.

## Dev commands
How to run, test, build.
```

### 2. `specs/<capability>.md` — one per identified functional area

Each file follows the capability spec format from `spec:core`. Every Decisions entry must have rationale. Invariants must be testable.

Seed ADRs (`specs/adr/ADR-001-slug.md`) only when a significant cross-cutting decision with a meaningful rejected alternative emerges. Do not create ADRs for straightforward choices.

### 3. `specs/README.md`

Brief descriptive text per capability — not a flat file index. A human reading it should understand what the project does and where to look for each area.

### 4. `CLAUDE.md` enforcement block

Append to an existing `CLAUDE.md`, or create it. Insert verbatim:

```markdown
## Spec-driven development

This project follows spec-driven development.
Specs are the source of truth. Code is a derivative.

Before implementing any feature, bugfix, or refactor:
1. Read `specs/project.md`
2. Read any relevant capability spec in `specs/`
3. If no spec exists for the area you're touching, create one before writing code
4. If specs exist but are outdated, update them before writing code

Use the following skills:
- `spec:core` — core principles, always available as reference
- `spec:bootstrap` — starting a new project or capability
- `spec:maintain` — updating specs alongside a change
- `spec:capture` — capturing decisions from the current session
- `spec:infer` — recovering specs from an existing codebase
- `spec:validate` — auditing spec completeness and invariant coverage

Run `spec:validate` before considering any feature branch complete.

## Specs
```

Then append a generated list of the capability specs just created, one line each: `- <capability>.md — <one sentence description>`.

### 5. `AGENTS.md`

Create with equivalent content to the `CLAUDE.md` enforcement block above. This ensures non-Claude agents in the same project follow the same spec-driven workflow.

## Common Mistakes

| Mistake | Correct behaviour |
|--------|-------------------|
| Writing specs from a vague brief | Evaluate quality bar first. Ask if any condition is unmet. |
| Asking all questions at once | One question per gap, in priority order. Wait for each answer. |
| Inferring tech stack from domain type | "Task manager" ≠ CLI. Ask. |
| Writing placeholder specs to unblock | Partial specs are worse than no specs. Block until clear. |
| Skipping CLAUDE.md / AGENTS.md | Always append the enforcement block. It's what makes spec-driven development self-reinforcing. |
| Creating ADRs for every decision | ADRs are for cross-cutting decisions with meaningful rejected alternatives. Not for every choice. |
