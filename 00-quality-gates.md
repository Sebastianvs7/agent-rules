# Quality Gates

Load for every code change.

## Rules

- Do not leave `console.log`, `console.warn`, `debugger`, or temporary debug code in committed code.
- Do not leave unused variables, imports, types, props, or functions.
- Prefer small, focused functions over large blocks of mixed logic.
- Extract repeated or complex logic into named helpers, hooks, or smaller components.
- Keep logic granular: each function/component should have one clear responsibility.
- Avoid unrelated refactors while making a targeted change.
- Use strict equality (`===` / `!==`); never `==` / `!=`.
- When JSX only shows or hides one node, write the presence check inline with `&&` (`label && <Button aria-label={label} />`). Do not stash it in a `boolean` such as `hasToggle`: that drops narrowing and `string | undefined` stops fitting props that require `string`. Do not "fix" that with `!== undefined` when `&&` already narrows. Details: [`08-jsx-and-props.md`](08-jsx-and-props.md).

## Final Self-Check

Before completing a change, verify:

- No debug logs or temporary debug code remain.
- No unused code remains.
- Comparisons use `===` / `!==`.
- A JSX presence check that renders one node is inline `&&`, not a `boolean` that drops narrowing.
- Repeated UI patterns are represented by one shared component.
- If the project has i18n, new user-facing text is in the catalog, not hardcoded.
- Logic is split into small, understandable units.
- The loaded task-specific rules have been checked.
