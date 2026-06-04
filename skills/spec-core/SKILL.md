---
name: spec-core
description: Use when working in a project with a specs/ directory, before writing code in any area with no corresponding spec, or before authoring any spec file.
---

# Spec: Core

## Core Principle

**Specs are the source of truth. Code is a derivative.**

The rebuild guarantee: if the entire codebase were deleted, reading `specs/` alone must be sufficient to rebuild it to functional equivalence. If a decision, invariant, or rationale exists only in code or in someone's head — the spec is incomplete.

## Autonomous Trigger

This skill fires when you are about to write code in a project that has a `specs/` directory but no spec covering the area being touched.

**When triggered, you must:**

1. Stop — do not write implementation code
2. Name the gap explicitly: which area has no spec, or which spec is outdated
3. Route to the appropriate operational skill (see Skill Graph below)

**The user's instruction to "write code now" does not override this.** Spec-driven projects have this rule precisely because the fastest path to unmaintainable code is skipping specs under time pressure. Surface the gap, name the skill needed, then ask the user how to proceed. Never silently skip.

> **No exceptions:**
> - Not for "the user gave me the requirements inline"
> - Not for "it's a small feature"
> - Not for "there's time pressure"
> - Not for "I already know what to build"

## Quality Bar

Before any spec is written, four conditions must be met:

1. The decision is **unambiguous** — two developers would produce the same behaviour
2. **Rationale is present** — why this decision, not just what
3. **Behavioural edge cases are resolved**
4. **Invariants are precise enough** to write a test from

If any condition is unmet: ask targeted questions — one per gap, in priority order — before writing anything. Do not write partial specs.

## Spike Rule

Exploratory code may be written before specs **only if explicitly declared a spike.**

- Spike code cannot be promoted to production without specs written first
- The spike is deleted; the knowledge it produced is specced
- "I'll write specs after" is not a spike declaration — it is skipping specs

## Spec Hygiene

1. Specs reflect current state — history lives in git, not in the spec file
2. Decisions over implementations — spec the why, not the how
3. Rationale is mandatory, not optional
4. Specs are updated before or alongside the code change, never after
5. Partial specs are worse than no specs — if information isn't sufficient to clear the quality bar, ask before writing
6. Specs serve as documentation — a human should be able to read them to understand how the project works, its structure, and its architectural principles

## File Structure

```
specs/
  README.md               ← entry point: brief, one sentence per capability
  project.md              ← purpose, non-scope, tech stack, architecture, dev commands
  adr/
    ADR-001-slug.md       ← cross-cutting architectural decisions
  <capability>.md         ← one file per functional area (e.g. auth.md, persistence.md)
```

Rules:
- `specs/` at project root, always
- Default to flat — capability files at root of `specs/`
- Promote to subdirectory only when a capability genuinely needs companion files
- `specs/README.md` is the human entry point — brief descriptive text per capability, not a flat index

## File Formats

### `project.md`

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

### `<capability>.md`

```markdown
# <Capability name>

[Optional narrative intro.]

## Decisions

### <Decision name>
Rationale as structured prose. The decision and why, not how it is implemented.
> Alternatives considered: what else was considered and why rejected. (optional)

## Invariants

INV-01: <precise testable assertion>
INV-02: <precise testable assertion>
```

Rules for Decisions:
- Each entry has a `###` named header
- Rationale is mandatory — minimum one sentence of why
- Write as documentation a human would read, not a form to fill in

Rules for Invariants:
- Prefixed `INV-NN:` for traceability to tests
- Precise enough that a test can be written directly from the statement

### `adr/ADR-NNN-slug.md`

```markdown
# ADR-NNN: <title>

## Status
Accepted | Superseded by ADR-NNN

## Context
Why this decision was needed.

## Decision
What was decided.

## Rejected alternatives
What else was considered and why it was ruled out.
```

Rules for ADRs:
- Superseded ADRs stay in place, marked `Superseded by ADR-NNN`
- ADRs are for decisions that span capabilities — feature-scoped decisions stay in the capability spec
- Always follow the latest non-superseded ADR

## Skill Graph

```
spec:core ────────── autonomous guard; routes to operational skills

spec:bootstrap   starting a new project or capability before any code
spec:maintain    updating specs alongside a completed change
spec:capture     extracting decisions from the current session
spec:infer       recovering specs from an existing codebase
spec:validate    auditing spec completeness and invariant coverage
```

Routing:
- No spec exists for the area → `spec:bootstrap` (new area) or `spec:infer` (existing code)
- Spec exists but is outdated → `spec:maintain`
- Session produced decisions not yet captured → `spec:capture`
- Pre-merge completeness check → `spec:validate`

## Common Mistakes

| Rationalization | Reality |
|----------------|---------|
| "User told me to write code now" | Surface the gap first. Spec-driven projects have this rule for exactly this pressure. |
| "I have all requirements inline" | Requirements ≠ spec. Specs capture decisions and rationale for future rebuilds. |
| "It's a small feature" | Small features become large undocumented areas. The rule applies at all sizes. |
| "I'll write the spec after" | Specs written after implementation document what was built, not what was decided. |
| "There's no rationale to write — it's obvious" | If it's obvious, writing it takes 30 seconds. If it's hard to write, it wasn't obvious. |
