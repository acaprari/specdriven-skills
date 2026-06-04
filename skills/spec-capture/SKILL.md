---
name: spec-capture
description: Use when a session has produced decisions, rationale, or context not yet captured in specs, and the session history is available to read.
---

# Spec: Capture

## Overview

Capture extracts decisions from the current session and writes them into spec files. It never writes silently — it presents what it found, flags ambiguities, and asks for confirmation before touching any file.

## Protocol

### Step 1: Extract and group

Read the session history. Group findings by capability area:

- Explicit decisions: something the user stated or chose, with rationale present or inferable from context
- Ambiguous items: looks like a decision, but intent is unclear — could be a preference, a temporary shortcut, or a decision that was never actually finalised
- Implementation details that were never decided: things that emerged from code rather than from a choice (these do not belong in specs)

### Step 2: Present before writing

Present findings to the user **before writing anything**:

> "Here's what I extracted from this session, grouped by area. Confirm and I'll write — or correct anything that's wrong."
>
> **[Capability area]**
> - Decision: [what was decided] — Rationale: [why]
> - Decision: [what was decided] — Rationale: [why]
>
> **Flagged — intent unclear:**
> - "[quote or paraphrase]" — Did you mean [interpretation A] or [interpretation B]?
>
> **Not capturing (implementation detail, no explicit decision):**
> - [thing that emerged from code] — If this was a deliberate choice, tell me the rationale and I'll add it.

Wait for confirmation before writing.

### Step 3: Write after confirmation

Create or update capability spec files based on confirmed decisions only. Follow the format from `spec:core`:

- New capability area → new `<capability>.md`
- Existing area → edit Decisions and Invariants entries in place
- Cross-cutting decision with meaningful rejected alternative → new ADR
- New capability files created → update `specs/README.md`
- `CLAUDE.md` enforcement block missing → add it

## What to Flag

Flag anything before writing where the intent could plausibly be misread:

- Statements made under time pressure that may have been shortcuts, not decisions
- "For now" qualifiers — is the constraint intentional or a deferred decision?
- Choices that look like defaults — was this actually considered, or just the first option tried?
- Disagreements or reversals mid-session — what was the final state?

Do not infer silently. Surface the ambiguity and ask.

## The Wall: Thin or Compacted Session Context

If the session context is thin, already compacted, or appears to be from a previous conversation:

> "The session context I can read [describes what's available]. I was able to recover [X decisions], but I can't reliably recover [Y] — the context doesn't contain enough detail. For those gaps, `spec:infer` can read the codebase directly and propose what's missing."

State what was recoverable and what was not. Do not guess at missing context.

## Soft Referrals

- Thin or compacted context → recommend `spec:infer`
- Ambiguous intent that questions can't resolve → recommend `spec:infer` as a complement (code may reveal more)

## Common Mistakes

| Mistake | Correct behaviour |
|--------|-------------------|
| Writing spec files before presenting findings | Extract → present → confirm → write. Always. |
| Including implementation details as decisions | Implementation details without rationale do not belong in specs |
| Writing ambiguous items without flagging them | Flag with explicit question; do not infer intent |
| Silently skipping items with unclear intent | Surface them in the "Flagged" section |
| Writing from compacted/prior session context without saying so | State the limitation; recommend `spec:infer` for gaps |
