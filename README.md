# Personal coding rules

Markdown rules for coding agents. Files in this directory are the source of truth. Do not copy them into app repos.

This repo does not know where it lives. Checkout path stays off git — only local pointer files (outside this repo) name a location.

## Attach (local, not committed)

Symlink this directory to `~/.agent-rules`, then point tools at that. If you move the clone, retarget the symlink; do not edit this repo.

```sh
ln -sfn "$(pwd)" ~/.agent-rules
```

Pointer text (same for each tool): read `~/.agent-rules/AGENTS.md` at the start of a coding task, then load only the matching topic files. Resolve those files relative to `~/.agent-rules/`.

- Cursor: `~/.cursor/rules/personal-rules.mdc` with `alwaysApply: true`
- Claude Code: `~/.claude/CLAUDE.md`
- Codex: `~/.codex/AGENTS.md`

## Layout

- `AGENTS.md` — always-on map (`00-quality-gates.md`, then matching topic files)
- `00-quality-gates.md` … `10-design-system-variants.md` — task files

If a rule in this pack conflicts with the current project, the project wins.
