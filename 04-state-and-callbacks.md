# State And Callback Rules

Load when changing React state, props, event callbacks, parent-child data flow, controlled components, or render props that expose actions.

## Rules

- Never pass setters such as `setValue`, `setItems`, or `setOpen` as props to children.
- Wrap setter logic in semantic callback functions before passing it down.
- Prefer callback prop names that describe the event or intent:
  - `onValuesChange`
  - `onItemRemove`
  - `onDialogOpenChange`
  - `onFiltersChange`
- Parent components should own state changes when the state belongs to the parent.
- Child components should receive values and intent-based callbacks, not implementation details.

## Example

```tsx
// BAD
<Child setOpen={setOpen} setItems={setItems} />

// GOOD
<Child
  isOpen={isOpen}
  items={items}
  onItemsChange={handleItemsChange}
  onOpenChange={handleOpenChange}
/>
```

## Emit Semantic Values, Not Raw DOM Details

For selection, toggle, and similar controlled shells that only exist to present a choice:

- Prefer `onChange(value)` (or an intent callback with the chosen value) over forwarding the raw DOM event when the shell already knows the value.
- Do not expose DOM plumbing props (`id`, native event shape) that the shell resolves internally (e.g. input wrapped in `<label>`).

```tsx
// BAD — caller must dig value out of the event
onChange: (event: React.ChangeEvent<HTMLInputElement>) => void;

// GOOD — caller receives the selected value
onChange: (value: string) => void;
```

## Self-Check

- No state setters are passed to children.
- Callback props use `on...` names.
- Internal handlers use `handle...` names.
- Callback props describe intent, not implementation.
- Selection/toggle shells emit values (or intent), not raw events the caller must parse.
- Unnecessary DOM props the shell already owns are not part of the public API.
