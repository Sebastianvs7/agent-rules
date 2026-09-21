# Git: Commits and Rebase

Load when committing, amending, rebasing/splitting history, creating or updating a PR, merging to main, or reviewing a large branch diff.

## Split Mixed Commits

- One commit = one cohesive concern (not an entire feature across shared code, a feature module, and wiring).
- Before committing or opening a PR, check `git diff --stat`. If a commit spans multiple layers (e.g. shared UI + a feature module + wiring), split it.
- Split by what changed:
  - shared building blocks
  - feature integration (wiring, validators, feature-specific copy)
- To fix an already-pushed mono-commit: soft-reset → stage by area → recreate N commits → `force-with-lease` only when the user wants to rewrite the branch.

```text
# BAD — one mega-commit across layers
feat: add search  (~50+ files: shared UI + feature + wiring)

# GOOD — one concern per commit
feat: add shared search input
feat: wire search into the list page
```

## Rebase for Clean History

- Regularly rebase the feature branch onto current `main` (do not merge `main` into the feature branch) so history stays linear and merging to `main` has fewer conflicts.
- PR history should contain meaningful commits (one concern / one intent), not a trail of tiny fixes (`fix typo`, `oops`, `one more thing`).
- Fold small follow-ups (whitespace, dep order, tiny same-file fixes) into the correct existing commit (amend / soft-reset + recommit). Do not add a new commit just for a trivial tweak.
- After rewriting history, push with `force-with-lease` only when the user explicitly wants that.

## Merge To Main

- Prefer the **rebase** merge strategy when landing PRs on `main` (not a merge commit), so history stays linear.
- When several related PRs touch shared docs, merge **one PR at a time**, then rebase the next branch onto updated `main` before merging it. That keeps conflict resolution small and sequential.

## General

- Do not commit editor or agent folders (`.cursor/`, `.claude/`, …) unless the project already tracks them.
- Do not add AI co-author / attribution to commit messages unless the user asks for it.
- After rewriting history, update the PR description so it matches the resulting commits.
