# Formatting Rules

Load when editing JavaScript, TypeScript, JSX, TSX, JSON-like objects, imports, props, arrays, or multiline code.

## Rules

- Match the project's formatter and neighboring files. Do not fight Prettier, ESLint, or an editorconfig that already exists.
- If the project has no formatter:
  - End JavaScript and TypeScript statements with a semicolon.
  - Use single quotes for JavaScript and TypeScript strings, including imports.
  - Use double quotes for HTML attributes and JSX attributes.
  - Use trailing commas for multiline objects, arrays, function arguments, and destructured values.
  - End every file with exactly one newline.
- Break long lines into readable multiline structures.
- Destructure props across multiple lines when the list is long.
- Break large object literals, arrays, JSX props, and function parameters.
- Sort object keys, type members, destructured props, and JSX props alphabetically; put handlers (`on*`) last. Details: [`02-typescript.md`](02-typescript.md), [`08-jsx-and-props.md`](08-jsx-and-props.md).
- The linter enforces A–Z only for interface/type members and JSX props. Object literals are not linted: sort their keys by hand in new code, but never move a key across a `...spread` or where evaluation order matters.

```ts
// BAD — members not alphabetical
export interface ButtonProps {
  className?: string;
  children?: React.ReactNode;
}

// GOOD — members alphabetical
export interface ButtonProps {
  children?: React.ReactNode;
  className?: string;
}
```

## Blank lines between logical blocks

Applies to statements in any TypeScript or JavaScript, not only JSX. The linter enforces it (`padding-line-between-statements`).

- Blank line before `return`, `throw`, `break`, and `continue`.
- Blank line after a group of `const`/`let`/`var` declarations; consecutive declarations stay together.
- Blank line before and after multiline blocks (`if`, `for`, `switch`, ...), multiline expressions, function and class declarations, and `try`.
- Blank line after an `if` statement, except when the next statement is another `if`.

```ts
// BAD — return glued to the declaration
const faq = tool.faq ?? toolFaqs[tool.id];
return withDefaultKeywords({ ...tool, faq });

// GOOD
const faq = tool.faq ?? toolFaqs[tool.id];

return withDefaultKeywords({ ...tool, faq });
```

```ts
// BAD — nested functions, guard, and listeners all run together
const timer = setTimeout(() => finish(new Error(REGEX_TIMEOUT_MESSAGE)), 2_000);
function finish(error?: Error): void {
  if (settled) return;
  settled = true;
  clearTimeout(timer);
}
worker.addEventListener('message', handleMessage);
if (signal?.aborted) {
  handleAbort();
  return;
}
try {
  worker.postMessage(message);
} catch (error) {
  finish(error instanceof Error ? error : new Error(String(error)));
}

// GOOD
const timer = setTimeout(() => finish(new Error(REGEX_TIMEOUT_MESSAGE)), 2_000);

function finish(error?: Error): void {
  if (settled) return;

  settled = true;
  clearTimeout(timer);
}

worker.addEventListener('message', handleMessage);

if (signal?.aborted) {
  handleAbort();

  return;
}

try {
  worker.postMessage(message);
} catch (error) {
  finish(error instanceof Error ? error : new Error(String(error)));
}
```

## Long text and long functions

- Lines stay within 100 characters. The linter ignores string literals, template literals, URLs, and regex literals, so it will not flag a long message: put user-facing text longer than 100 characters into a named module-level constant on its own line instead of an inline string.
- Functions stay within 60 lines: that is a lint error (existing violations are held in `eslint-suppressions.json`). 100 lines is a hard ceiling that suppressions cannot hide. Split longer functions into named steps.

```ts
// BAD — long message inline
setTimeout(() => finish(new Error('Regex exceeded the 2-second execution limit. The isolated worker was stopped. Simplify the pattern or reduce the input, then run again.')), 2_000);

// GOOD
const REGEX_TIMEOUT_MESSAGE =
  'Regex exceeded the 2-second execution limit. The isolated worker was stopped. Simplify the pattern or reduce the input, then run again.';

setTimeout(() => finish(new Error(REGEX_TIMEOUT_MESSAGE)), 2_000);
```

## Self-Check

- Formatting matches the project (or the no-formatter defaults above).
- Long lines are broken cleanly.
- Type members and props are alphabetical (lint-enforced), object keys sorted by hand in new code (not across spreads or order-sensitive code), with `on*` handlers last.
- Blank lines separate logical blocks: before `return`/`throw`/`break`/`continue`, after declaration groups, around multiline blocks, nested functions, and `try`.
- Long user-facing text is a named constant; functions are at most 60 lines.
