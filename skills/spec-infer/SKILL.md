---
name: spec-infer
description: Use when adding specs to an existing codebase with no specs, when specs are suspected outdated, or when referred from spec:capture due to thin session context.
---

# Spec: Infer

## Overview

Infer recovers specs by reading the codebase. It is the highest-risk skill for silent misinterpretation — code reveals what was built, not what was decided. Every inferred decision is marked until the user confirms it. Nothing is promoted from implementation detail to spec entry without explicit rationale.

## Scope First

Before reading any code, confirm scope with the user:

> "I'll start with [capability area]. Is that the right scope, or do you want me to cover other areas? On a broad codebase I'll work one capability area at a time and check in after each."

Do not start inferring across the whole codebase without confirmation.

## The Inference Rule

For every candidate decision in the code, ask: **could this plausibly be accidental?**

- If yes → mark as `> Inferred — please confirm` and surface the ambiguity
- If no (unambiguously intentional and well-understood pattern) → write as a decision with rationale from context

When in doubt, mark and ask. Unmarked inferences that turn out to be wrong are harder to find than marked ones.

## Output Format

Write capability spec files following the format from `spec:core`. For every entry where rationale is missing or the decision could be accidental:

```markdown
### <Decision name>
[What the code does.]
> Inferred — please confirm: was this a deliberate decision? If so, what was the rationale?
```

For entries where rationale can be inferred with confidence (well-known security patterns, etc.):

```markdown
### <Decision name>
[What was decided and why — rationale inferred from context.]
> Alternatives considered: [if a well-known alternative exists and was visibly rejected in the code]
```

Invariants follow the same rule: if a behaviour could be accidental, mark it.

```markdown
INV-01: <assertion> *(inferred — please confirm)*
```

## Missing Rationale

When code reveals a decision but cannot reveal why:

> "I can see [behaviour X] in the code but I don't know why it was chosen over [obvious alternative Y]. I'll mark this entry as incomplete — if the rationale exists in session history, `spec:capture` may be able to recover it."

Mark the entry and continue. Do not invent rationale.

## What Does NOT Belong in Specs

Do not promote to a Decision entry:
- The specific library used (unless the choice of library is itself the decision)
- Internal variable names, function signatures, file structure
- Behaviour that looks like a default that was never considered
- Anything whose only evidence is that it's in the code

These are implementation details. If in doubt, ask before writing.

## Temporary Markers

`> Inferred — please confirm` markers are temporary scaffolding. They signal that a human has not yet validated the entry. `spec:validate` flags any remaining unconfirmed markers as open gaps. The goal is zero unconfirmed markers in a mature spec.

## Soft Referrals

- Missing rationale → recommend `spec:capture` (session context may have it)
- Ambiguous intent → ask first, infer second

## Common Mistakes

| Mistake | Correct behaviour |
|--------|-------------------|
| Writing inferred decisions without markers | Every plausibly-accidental decision gets `> Inferred — please confirm` |
| Starting inference across the whole codebase | Confirm scope one capability area at a time |
| Inventing rationale from patterns | Mark as incomplete; recommend `spec:capture` for rationale recovery |
| Promoting implementation details to decisions | Ask "is this a deliberate decision?" before writing it as one |
| Treating "it works" as "it was decided" | Working code and decided behaviour are not the same thing |
