# Git: Commits, Branches, Rebase, and PRs

Load when committing, branching, amending, rebasing/splitting history, resolving rebase conflicts, creating or updating a PR, merging to main, or reviewing a large branch diff.

## Discover Before Acting

- Before branching, rebasing, or opening a PR, check the state and the base branch. Never assume `main`.
  - `git status` (clean tree? merge/rebase/cherry-pick in progress? then stop and ask)
  - `git branch --show-current`
  - `gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name'`
  - `git log --oneline -10` to match the repo's message style and ticket format
- Repo conventions (CONTRIBUTING, PR template, commitlint, hooks) win over this file.

## Branches

- Follow the repo's branch naming. If none exists: `type/short-description` (`feature/`, `fix/`, `chore/`, `refactor/`, `docs/`), kebab-case, optional ticket ID (`feature/date-range-field-PRO-208`).
- Start a branch from up-to-date base (`git fetch origin` first).
- Do not create or switch branches unless the task needs it or the user asks.

## Stage Explicitly

- Stage files by name: `git add path/a.ts path/b.ts`. Do not use `git add -A`, `git add .`, or `git commit -a`.
- Before committing, verify: `git status` and `git diff --cached --stat`. Only intended files may be staged.
- Never commit secrets, `.env*`, credentials, tokens, keys, or large binaries. If a staged file looks sensitive, stop and warn the user.

## Commit Messages

- Conventional Commits without scope: `type: subject`. Never add a `(scope)` after the type.
- Types: `feat`, `fix`, `refactor`, `perf`, `test`, `docs`, `style`, `chore`, `build`, `ci`.
- Subject: imperative mood, lowercase start, no trailing period, under 72 characters (aim for ~50).
- Ticket reference: match the repo's existing format (e.g. `(PRO-208)` at the end of the subject).
- No generic subjects (`update code`, `fix bug`, `changes`, `wip`). The subject says *what* changed in domain terms.
- Body is optional. Add one only when the *why* is non-obvious (tradeoff, root cause, external context). Use a HEREDOC for multi-line messages:

```bash
git commit -m "$(cat <<'EOF'
fix: keep selected negative targets in the activity form (PRO-305)

The form reset dropped targets not present in the current page of
options, so saved negative targets disappeared on edit.
EOF
)"
```

- Breaking change: `type!: subject` plus a `BREAKING CHANGE:` footer.

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

## Checkpoint Commits

- During long agent tasks, WIP checkpoint commits are fine (`wip: ...`), but keep them logically grouped.
- Before opening a PR, fold every WIP commit into the final clean commits. No `wip:` commit may reach the PR.

## Rewriting History Safely

- Before any rebase, soft-reset, or history split, create a local backup: `git branch backup/<branch>-<yyyymmdd>` (do not push it unless asked).
- After rewriting, prove nothing was lost: `git diff backup/<branch>-<yyyymmdd> HEAD` must be empty (unless the rewrite intentionally changed content).
- Before any history-rewriting command on a pushed branch (`git rebase`, `git reset`, `git push --force*`), show the exact command and wait for confirmation.
- Never run `git reset --hard`, `git clean -fd`, `git checkout -- .`, or `git branch -D` without explicit confirmation. Prefer `git rebase --abort` to back out.

## Rebase for Clean History

- Regularly rebase the feature branch onto current `main` (do not merge `main` into the feature branch) so history stays linear and merging to `main` has fewer conflicts.
- `git fetch origin` then `git rebase origin/<base>`. Use `--update-refs` when other local branches are stacked on this one, then push each moved branch separately.
- PR history should contain meaningful commits (one concern / one intent), not a trail of tiny fixes (`fix typo`, `oops`, `one more thing`).
- Fold small follow-ups (whitespace, dep order, tiny same-file fixes) into the correct existing commit (amend / `git commit --fixup <sha>` + `git rebase --autosquash`). Do not add a new commit just for a trivial tweak.

### Resolving Conflicts

- Read the surrounding code and both intents before editing a conflict. Prefer minimal, mechanical resolutions.
- Keep upstream changes unless the branch deliberately supersedes them.
- Regenerate generated files (lockfiles, codegen, GraphQL types) instead of hand-merging them.
- If the correct resolution is unclear, stop and ask one targeted question. Do not invent product behavior.
- Stage only resolved files, then `git rebase --continue`.
- After the rebase: `git log --oneline origin/<base>..HEAD`, then run the project's checks (lint, typecheck, tests).

## Push

- Plain `git push` by default. Rewritten history: `git push --force-with-lease`, never plain `--force`, and only after the user confirms.
- Never push to `main` directly.

## Pull Requests

- Confirm the target branch with the user before creating the PR.
- Run the project's checks (lint, typecheck, tests, build as the repo requires) before opening the PR. Report failures; do not open a "ready" PR with red checks.
- Use the repo's PR template (`.github/pull_request_template.md`) and fill every section; write `—` for sections that do not apply.
- Title: Conventional Commits format, matching the dominant commit type and the repo's ticket format, under 70 characters.
- Body explains what changed, why, and how it was verified (checklist). List breaking changes explicitly.
- Show the title and body to the user for approval before `gh pr create`. Pass the body via HEREDOC.
- Use `--draft` when the PR is not ready for review.
- A PR must be reviewable as one coherent unit. If a reviewer cannot hold the change in their head, propose splitting it.

## Merge To Main

- Prefer the **rebase** merge strategy when landing PRs on `main` (not a merge commit), so history stays linear.
- Before merging: CI is green, review comments are resolved or answered, and the user confirmed the merge.
- When several related PRs touch shared docs, merge **one PR at a time**, then rebase the next branch onto updated `main` before merging it. That keeps conflict resolution small and sequential.
- After merging: sync local base (`git checkout main && git pull`), delete the merged topic branch. Never delete `main` or other long-lived branches.

## General

- Do not commit editor or agent folders (`.cursor/`, `.claude/`, …) unless the project already tracks them.
- Do not add AI co-author / attribution to commit messages unless the user asks for it.
- After rewriting history, update the PR description so it matches the resulting commits.
