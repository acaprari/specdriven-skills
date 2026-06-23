# specdriven-skills

A Claude Code skill suite for spec-driven development.

> **Specs are the source of truth. Code is a derivative.**
> If the entire codebase were deleted, Claude must be able to rebuild it
> to functional equivalence by reading `specs/` alone.

## The six skills

| Skill | Purpose | When to invoke |
|---|---|---|
| `spec:core` | Core principles, file formats, skill routing | Fires autonomously when code is about to be written with no spec for the area |
| `spec:bootstrap` | Create the spec foundation for a new project or capability | Start of a new project, before any code |
| `spec:maintain` | Update specs alongside a completed change | After finishing a feature, bugfix, or refactor |
| `spec:capture` | Extract decisions from the current session | When a session has produced decisions not yet in specs |
| `spec:infer` | Recover specs from an existing codebase | Adding specs to an existing project; when specs are outdated |
| `spec:validate` | Audit spec completeness and invariant coverage | Before merging any feature branch |

## How it works

Once installed, `spec:core` fires autonomously whenever Claude is about to write code in a project that has a `specs/` directory but no spec covering the area being touched. It surfaces the gap, blocks implementation, and routes to the appropriate skill.

The other five skills are invoked explicitly at the right moments in your workflow:

```
New project          →  spec:bootstrap
After a change       →  spec:maintain
Decisions in session →  spec:capture
Existing codebase    →  spec:infer
Before merging       →  spec:validate
```

## What gets created

Running `spec:bootstrap` on a new project produces:

```
specs/
  README.md          ← human entry point, one sentence per capability
  project.md         ← purpose, non-scope, tech stack, architecture, dev commands
  <capability>.md    ← one file per functional area
  adr/               ← architectural decision records (when needed)
CLAUDE.md            ← enforcement block directing Claude to read specs first
```

Capability specs follow a consistent format — Decisions (with rationale) and Invariants (precise enough to write a test from). See `spec:core` for the full file format reference.

## Install

Add the marketplace, then install the plugin:

```
/plugin marketplace add acaprari/specdriven-skills
/plugin install specdriven-skills@specdriven-skills
```

Or install directly from the repo in one step:

```
/plugin install github:acaprari/specdriven-skills
```

Then reload:

```
/reload-plugins
```

## Quick start

```
# New project
/spec:bootstrap

# After finishing a feature
/spec:maintain

# Before merging
/spec:validate
```

## License

MIT — see [LICENSE](LICENSE).
