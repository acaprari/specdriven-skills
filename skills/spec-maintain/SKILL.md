---
name: spec-maintain
description: Use when a feature, bugfix, or refactor is complete and specs need updating, or when a commit is about to happen without a corresponding spec update.
---

# Spec: Maintain

## Overview

Maintain updates specs to reflect a completed change. It does not scan the diff and silently infer — code reveals what changed, not why, and cannot surface decisions that were considered and rejected. Confirmation comes first.

## Confirm Before Touching Specs

Before writing any update, present your understanding of what changed and ask three targeted questions:

**1. Scope confirmation**
"I see this change touched [list of areas]. Did it affect any other capability I've missed?"

**2. Decisions not in the diff**
"Were any decisions made during this change that aren't visible in the code? (Rationale for choices, alternatives considered and rejected, constraints discovered.)"

**3. Invariants**
"Did any invariants change — behaviour that was previously guaranteed and no longer is, or new guarantees the code now upholds?"

Wait for answers. Do not write until you have them.

**Why confirmation is required:** Code shows what was built, not what was decided. A diff cannot reveal a rejected alternative, a constraint discovered mid-implementation, or a deliberate choice that looks identical to a default. Specs that omit rationale and rejected alternatives fail the rebuild guarantee.

## Output (after confirmation)

Update only what changed. Do not touch unrelated sections.

- **Updated `## Decisions` entries** — edit in place, not appended. Remove outdated content. Rationale is mandatory.
- **Updated `## Invariants`** — add, remove, or revise based on confirmed changes. Keep `INV-NN:` numbering stable where possible.
- **New `<capability>.md`** — if the change introduced a new functional area with no existing spec.
- **New ADR** (`specs/adr/ADR-NNN-slug.md`) — if the change involved a significant cross-cutting decision with a meaningful rejected alternative.
- **Updated `specs/README.md`** — only if new capability files were created.

If no spec update is needed, state this explicitly. Do not silently do nothing.

## The Wall: Outdated Specs Beyond This Change's Scope

While updating, you may notice that existing spec sections are significantly outdated in ways unrelated to the current change. Do not silently fix them. Do not ignore them.

Surface the gap explicitly:

> "While updating [area], I noticed [specific section] appears outdated — it describes [X] but the code does [Y]. That's outside the scope of this change. I'd recommend running `spec:infer` on that capability, or `spec:validate` to scope the full gap first."

Then continue with the current change only.

## Soft Referrals

- Scope of change is unclear → ask the targeted questions above before touching anything
- Gap found in the area being touched → recommend `spec:infer` for that capability
- Broad gaps across multiple areas → recommend `spec:validate` to scope the problem first, then `spec:infer`

## Common Mistakes

| Mistake | Correct behaviour |
|--------|-------------------|
| Scanning diff and writing updates silently | Present understanding, ask three questions, wait for answers |
| Inferring rationale from code | Ask "were any decisions made not visible in the diff?" |
| Silently fixing outdated specs beyond scope | Surface as a distinct gap, continue with current change only |
| Appending new decisions to end of file | Edit existing entries in place; specs reflect current state, not history |
| Doing nothing when no update is needed | State explicitly that no spec update is required |
| Writing a new ADR for every decision | ADRs are for cross-cutting decisions with meaningful rejected alternatives only |
