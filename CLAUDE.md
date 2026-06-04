# specdriven-skills — Contributor Guidelines

## What this repo is

A Claude Code skill suite for spec-driven development. Each skill lives in
`skills/<skill-name>/SKILL.md`. There are six skills: `spec:core`,
`spec:bootstrap`, `spec:maintain`, `spec:capture`, `spec:infer`,
`spec:validate`.

## Adding or modifying skills

Use `superpowers:writing-skills` for every addition or modification. The
full RED-GREEN-REFACTOR cycle is mandatory:

1. **RED** — write a pressure scenario and run it WITHOUT the skill. Document
   exact agent failures and rationalizations verbatim.
2. **GREEN** — write the skill addressing those specific failures. Run the
   same scenario WITH the skill. Verify the agent complies.
3. **REFACTOR** — run at least one harder scenario (loophole pressure). Close
   any new rationalizations. Re-verify.

**No skill without a failing test first.** This applies to new skills and to
edits of existing skills. No exceptions.

## Before committing

Show every new or modified skill to the user for review before committing.
One skill per commit.

## What not to commit

Any files generated during testing (e.g. spec files created in `/tmp`).
