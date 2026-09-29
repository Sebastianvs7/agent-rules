# JSX And Props Rules

Load when editing JSX, JSX conditions, props, prop spreading, or component render bodies.

## Rules

- Keep JSX readable by extracting complex conditions and computed values before `return`.
- Avoid deeply nested ternaries in JSX.
- Use named variables for non-trivial conditions.
- When a condition only decides whether to render one node, and the other outcome is nothing, use `&&`. The condition must be a boolean (or another value that is clearly only a presence check). Use a ternary when both branches are real values.
- Do not use `&&` when the condition can be `0` or `''`, or when a prop expects `string | undefined` and `false` would be the wrong value.
- Use strict comparison in JSX conditions: `===` / `!==`, never `==` / `!=`.
- Break JSX props onto multiple lines when there are several props or long values.
- Prefer explicit prop names over spreading unknown props, unless the component is intentionally forwarding props.

```tsx
// BAD — false branch is empty
{isOpen ? <Panel /> : null}

// GOOD — presence check renders the node
{isOpen && <Panel />}

// GOOD — both branches are real values
{isOpen ? <Panel /> : <Placeholder />}
label={hasItems ? 'Edit' : 'Add'}

// BAD — boolean drops narrowing; aria-label stays `string | undefined`
const hasToggle = Boolean(label) && hasContent;
{hasToggle && <Button aria-label={label} />}

// GOOD — `&&` narrows `label` to `string` for the node it renders
{label && hasContent && <Button aria-label={label} />}
```

## Prop Order

Apply the same order in JSX attributes, destructured props, and TypeScript prop types ([`02-typescript.md`](02-typescript.md)):

1. Non-handler names alphabetically.
2. Handler / callback props (`on*`) last, alphabetically among themselves.
3. Rest spread (`...rest`) last of all.

```tsx
// BAD
<Button
  onClick={handleSubmit}
  variant="primary"
  className="mb-4"
  disabled={isLoading}
/>

// GOOD
<Button
  className="mb-4"
  disabled={isLoading}
  variant="primary"
  onClick={handleSubmit}
/>
```

## Logical Blocks And Paired Elements

- Put a blank line between logical JSX blocks (e.g. after a `</header>` before the next conditional body).
- When an element has children, put the opening tag, children, and closing tag on separate lines for readability.

```tsx
// BAD — no separation; paired tags cramped
<header>…</header>
{isOpen && <Body />}
<div>content</div>

// GOOD
<header>…</header>

{isOpen && <Body />}

<div>
  content
</div>
```

## Self-Check

- Complex JSX conditions were extracted into named variables.
- No deeply nested ternaries remain in JSX.
- Presence checks that render one node use inline `&&` so TypeScript narrows the value. They are not extracted into a `boolean` that drops `string | undefined`. Ternaries stay when both branches are real values.
- Comparisons in JSX use `===` / `!==`.
- Props are alphabetical, with `on*` handlers last and `...rest` last of all.
- Long JSX props are split across lines.
- Logical JSX blocks are separated by a blank line.
- Paired elements with children use separate lines for open/children/close.
- Prop spreading is intentional and appropriate.
