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

## Self-Check

- Formatting matches the project (or the no-formatter defaults above).
- Long lines are broken cleanly.
- Keys, type members, and props are alphabetical, with `on*` handlers last.
