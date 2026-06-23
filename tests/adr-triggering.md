# ADR Triggering — Test Fixtures

Labeled scenarios for verifying that the ADR-worthiness heuristic in `spec:core`, `spec:maintain`, `spec:validate`, and `spec:capture` fires correctly: triggers on cross-cutting clusters, suppresses on capability-local or coincidence-of-timing changes, and avoids fragmentation at the per-commit cadence.

The corpus is shape-only — domain-neutral so it tests the heuristic, not memorization of specific codebases.

## How to use

Each case is a self-contained scenario describing a completed change (commit-level) or a finished branch (branch-level). Dispatch a subagent without project context, hand it the case shape as if it had just finished the change, and ask it to run the relevant skill. Check the agent's ADR proposal against the expected label.

- Commit-level cases → `spec:maintain`.
- Branch-level cases → `spec:validate`.

Expected outcomes assume the skills have been updated with:
- `spec:core` (a) + (b or c) heuristic for ADR-worthiness.
- `spec:maintain` fourth confirmation question with **defer** posture for mid-branch candidates.
- `spec:validate` category 10 — branch-level audit for missing or fragmented ADR clusters.
- `spec:capture` secondary cross-cutting-cluster pass.

## Aggregate cases (commit-level)

### Case A — Multi-commit branch revising a domain-specific DSL and shifting an integration from permissive to prescriptive (presented as an end-of-branch aggregate)

- Three capability specs updated across the branch: the language spec, the AI integration spec, the execution engine spec.
- Introduces a multi-tier age-progression *framework* that doesn't belong inside any single capability spec.
- Six rejected alternatives documented across the branch.
- One durable directional shift: permissive → prescriptive AI gating.

**Expected when presented as aggregate (via `spec:maintain` after branch consolidation, or `spec:capture` at session end):** ADR-worthy. One unified ADR. Passes (a), (b), and (c).

### Case B — Single commit consolidating a fragmented UX into one unified workspace, with a new visual system adopted at the same time

- Five capability specs touched.
- Six interlocking decisions presented as one coherent architectural shift.
- Rejected alternatives for each major axis.

**Expected:** ADR-worthy. Passes (a), (b), and (c). The interlock is the load-bearing argument — splitting across capability files would lose it.

### Case C — Documentation-only commit reconciling four capability specs with current code

- No behavioral change; no source touched.
- No new decisions; no rejected alternatives.
- Pure drift correction.

**Expected:** NOT ADR-worthy. Fails (b) and (c). Multi-spec scope alone is insufficient.

### Case D — Single refactor introducing a token system in one capability + a regression test

- One capability spec touched.
- Real directional choice (tokenization).
- No rejected alternatives surfaced; no impact on other capabilities.

**Expected:** NOT ADR-worthy. Fails (a). Capability `## Decisions` entry is sufficient.

### Case E — Single bugfix touching one source file, no spec change

**Expected:** NOT ADR-worthy. Trivial; included as a sanity-check negative.

### Case F — Late-arriving documentation for a cross-cutting UI primitive whose decision already lives in a prior ADR

- One capability spec touched.
- Underlying decision already captured in an existing ADR.

**Expected:** NOT ADR-worthy. Existing ADR holds the decision; this is capability-spec catch-up.

### Case G (borderline) — One commit touching two capability specs, but the changes are coincident independent bugfixes

- Two specs touched, no shared rationale, no rejected alternatives.

**Expected:** NOT ADR-worthy. Passes (a) but fails (b) and (c). Tests the false-positive risk of a naive "touches 2+ specs = ADR" rule.

### Case H (borderline) — Single-capability change with three rejected alternatives discussed

- One spec touched. Rejected alternatives are real but capability-scoped.

**Expected:** NOT ADR-worthy. Fails (a). Volume of alternatives is not the criterion. Tests over-firing on deliberation alone.

## Per-commit decomposition for branch-level cases

Case A and Case B can also be tested as per-commit sequences, simulating real per-commit `spec:maintain` invocations during a feature branch. The expected behavior changes: per-commit invocations should **defer** ADR authoring to branch-level review via `spec:validate`, rather than authoring multiple fragmentary ADRs.

### Case A — per-commit decomposition (6 commits)

| Commit | Shape | Per-commit expected outcome |
|---|---|---|
| A1 | Add multi-arg print form to the DSL. One spec; two rejected alternatives. | No ADR — capability-local. |
| A2 | Add named-parity conditions. One spec; three rejected alternatives. | No ADR — capability-local. |
| A3 | Add counted iteration + replace AST wrapper with direct alternatives. Two specs (language, execution engine); three rejected alternatives. | **Defer** — candidate ADR (cross-cutting), but mid-branch; do not author yet. |
| A4 | Add shared system-prompt constant for code-touching AI calls. One spec; one rejected alternative. | No ADR — capability-local. |
| A5 | Restructure per-level concept gating from permissive to prescriptive. One spec; three rejected alternatives. | **Defer** — candidate ADR (directional shift), but mid-branch; do not author yet. |
| A6 | Add five-rule constraint block to problem-generation prompt. One spec; three rejected alternatives. | No ADR — capability-local. |

### Case B — per-commit decomposition (4 commits)

| Commit | Shape | Per-commit expected outcome |
|---|---|---|
| B1 | Consolidate multi-screen play loop into single always-on workspace. Two specs; two rejected alternatives. | **Defer** — candidate ADR (cross-cutting); do not author mid-branch. |
| B2 | Adopt new visual system (gradient background + glass surfaces). One spec; two rejected alternatives. | **Defer** — candidate ADR (introduces a refraction-over-paint principle); do not author mid-branch. |
| B3 | Establish disciplined accent palette + adopt new typography. One spec; three rejected alternatives. | **Defer** — candidate ADR (framework: role-discipline, will constrain every future component); do not author mid-branch. |
| B4 | Combine success and branch-choice into one popup + desktop-only constraint. Two specs; two rejected alternatives. | **Defer** — candidate ADR (desktop-only is cross-cutting via guards); do not author mid-branch. |

### Branch-level review (after all per-commit commits)

After all per-commit `spec:maintain` invocations have run (each surfacing or suppressing candidate ADRs but not authoring any), `spec:validate` runs at branch close.

**Expected:**

- **Case A aggregate:** `spec:validate` category 10 detects the cluster (language + AI integration + execution engine, plus the multi-tier framework not native to any single capability, plus the permissive→prescriptive directional shift). Recommends ONE consolidated ADR via `spec:maintain`.
- **Case B aggregate:** `spec:validate` category 10 detects the cluster (workspace + visual-system + popup + viewport guard, with interlocking rationale). Recommends ONE consolidated ADR.

## Common rationalizations observed in baseline testing

Rationalizations the heuristic must explicitly suppress:

- "Three rejected alternatives = ADR." Volume of alternatives is not the criterion; cross-capability scope is.
- "Touched two specs = ADR." Multi-spec is necessary but not sufficient (Case G is the counter-example).
- "Big-sounding refactor (e.g. design tokens) = ADR." Single-capability work belongs in capability `## Decisions`.
- "Reconciliation across many specs = ADR." Documentation catch-up has no decision content (Case C).
- "Per-commit ADR per surfaced candidate = ADR." Per-commit ADR authoring fragments what should be one unified ADR; defer to branch-level review.
- "Defer every mid-branch commit just in case it becomes cross-cutting." Pre-emptive deferral produces the same fragmentation the defer protocol tries to prevent. Apply the heuristic to the present commit only; `spec:validate` cat 10 catches actual clusters at branch close.
- "Shared constant / single prompt rule / single DSL construct = framework/principle." A framework constrains other capabilities — capability-local features that hint at future cross-cutting work are not yet (a)-triggering.

## Notes for test maintainers

These fixtures encode shapes, not specific projects. When the heuristic evolves, update the expected-outcome columns and the rationalization list accordingly. Add new borderline cases when they surface in real use — borderline cases are the ones that drive heuristic refinement.
