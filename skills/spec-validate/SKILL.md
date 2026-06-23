---
name: spec-validate
description: Use when auditing spec completeness before merging a feature branch, when broad spec gaps are suspected, or when referred from spec:maintain or spec:core.
---

# Spec: Validate

## Overview

Validate audits the full `specs/` directory against the codebase and produces a structured gap report. It runs without clarification — audit first, questions after. Do not ask the user anything before producing the report.

## Severity Levels

- **Critical** — rebuild from specs would produce wrong behaviour
- **Gap** — rebuild would produce incomplete behaviour
- **Drift** — spec and code have diverged but rebuild intent is recoverable

## The Ten Categories

Check every category, in order. Do not skip any. Report findings under the appropriate severity.

### 1. Missing capability specs
Code areas with no corresponding spec file, or not listed in `specs/README.md`.
*How to find:* scan `src/` (or equivalent) for functional areas; cross-reference with `specs/`.
*Severity:* Critical if a core area; Gap if peripheral.
*Referral:* `spec:infer`

### 2. Outdated decisions
Spec entries contradicted by current code.
*How to find:* for each Decision entry, verify the described behaviour exists in code.
*Severity:* Critical if the contradiction would produce wrong rebuilt behaviour; Drift if intent is recoverable.
*Referral:* `spec:maintain` if from a recent change; `spec:infer` if drift is older or scope is unclear.

### 3. Unconfirmed inferences
Remaining `> Inferred — please confirm` markers in any spec file.
*How to find:* grep `specs/` for `> Inferred`.
*Severity:* Gap — the entry has not been validated by a human.
*Referral:* review directly with the user; `spec:infer` as complement if code provides more context.

### 4. Missing invariants
Capability specs that have a `## Decisions` section but no `## Invariants` section.
*Severity:* Gap.
*Referral:* none (spec gap, not a skill referral — add invariants when updating the spec).

### 5. Untested invariants
Invariants with no corresponding test in the test suite.
*How to find:* for each `INV-NN:` entry, search the test suite for coverage of that assertion.
*Severity:* Gap — spec is complete but rebuild guarantee is unverifiable.
*Referral:* none (testing gap, not a spec gap — surface as finding only).

### 6. Incomplete ADRs
ADR files missing required sections: Status, Context, Decision, Rejected alternatives.
*Severity:* Gap.
*Referral:* none.

### 7. Superseded ADRs still being followed
Code that follows a superseded ADR rather than the latest accepted one.
*How to find:* check each ADR marked `Superseded by ADR-NNN`; verify code follows the superseding ADR.
*Severity:* Critical if the superseded decision produces wrong behaviour; Drift if recoverable.
*Referral:* `spec:maintain`.

### 8. CLAUDE.md enforcement block missing or incomplete
`CLAUDE.md` (and `AGENTS.md`) must contain the spec-driven development section with the full skill list.
*Severity:* Gap.
*Referral:* `spec:bootstrap` (adds the block if never created).

### 9. specs/README.md out of sync
Files in `specs/` not listed in `specs/README.md`, or entries in `specs/README.md` with no corresponding file.
*Severity:* Drift.
*Referral:* none — update `specs/README.md` directly.

### 10. Unpromoted or fragmented cross-cutting ADR clusters
Branch-level audit. Per-commit `spec:maintain` invocations are structurally blind to cross-cutting clusters that emerge across commits, and may also fragment one cluster into multiple per-commit ADRs. This category catches both.

*How to find:*
- Walk commits since branch-from-main (`git log main..HEAD`).
- For each commit's capability spec edits, note: specs touched, presence of rejected alternatives, presence of directional-shift language ("from X to Y", "no longer", "moving from", "shifted to").
- Identify candidate ADR clusters: the *aggregate* set of capability specs touched across the branch is 2+ (or a framework/principle is introduced that lives in no single capability), AND a shared directional argument or a rejected-alternative cluster spans the commits.
- For each candidate cluster:
  - **No ADR exists** for it → **missing ADR** finding. The cluster needs one consolidated ADR.
  - **Multiple per-commit ADRs** each capture a fragment of one cluster → **fragmented ADR cluster** finding. Recommend consolidation into one ADR.
  - **A stand-alone ADR** cleanly captures the cluster per the `spec:core` (a) + (b or c) heuristic → clean.

*Severity:* Gap (missing ADR); Drift (fragmented cluster — consolidation recoverable).
*Referral:* `spec:maintain` to author the consolidated ADR; `spec:capture` to assemble the cross-cutting view from session history when available.

## Report Format

```
## Spec Validation Report

### Critical
[ID] [file] — [description]
→ [referral skill if applicable]

### Gap
[ID] [file] — [description]
→ [referral skill if applicable]

### Drift
[ID] [file] — [description]
→ [referral skill if applicable]

### Summary
X critical, Y gaps, Z drift items.
Recommended next step: [highest-priority action with skill referral]
```

If a category has no findings, omit it from the report. If all categories are clean, say so explicitly — do not silently produce an empty report.

## Soft Referrals Per Finding

| Finding type | Referral |
|-------------|---------|
| Missing or outdated capability specs | `spec:infer` |
| Unconfirmed inferences | Review with user; `spec:infer` as complement |
| Gaps from a recent change | `spec:maintain` |
| Broad gaps across multiple areas | `spec:infer` scoped by area |
| Missing tests for invariants | Surface as finding — no skill referral (testing gap) |
| Missing CLAUDE.md block | `spec:bootstrap` |
| Missing or fragmented cross-cutting ADR cluster | `spec:maintain` (to author the consolidated ADR); `spec:capture` (if session context can supply the cross-cutting view) |

## Common Mistakes

| Mistake | Correct behaviour |
|--------|-------------------|
| Asking clarifying questions before running | Audit mode — produce report first, questions after |
| Stopping after finding the first issue | Check all ten categories, every time |
| Reporting without skill referrals | Every finding gets a referral where one applies |
| Silently skipping categories with no findings | Report clean categories as clean, or omit cleanly — never silently skip |
| Mixing severity levels inconsistently | Critical = wrong rebuild; Gap = incomplete rebuild; Drift = recoverable divergence |
