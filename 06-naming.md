# Naming Rules

Load when adding or renaming handlers, callback props, boolean values, variants, helpers, components, hooks, or variables.

## Handlers And Callbacks

- Internal event handlers must use `handle{Action}` names.
  - Good: `handleRemove`, `handleAdd`, `handleSubmit`, `handleOpenChange`.
  - Bad: `onClickHandler`, `removeHandler`, `submitFunction`.
- Callback props must use `on{EventOrAction}` names.
  - Good: `onRemove`, `onAdd`, `onSubmit`, `onValuesChange`.

## Booleans Name The Exception

- Boolean names should read as booleans: `isOpen`, `hasError`, `canSubmit`, `shouldRender`.
- Name the **deviation from the default**, not the default itself. If the component is shown by default, prefer `collapsed` over `visible` / `displayed`.
- Align the name with existing UX language in the project (collapse/expand, not visible/hidden) when that language is already established.

```tsx
// BAD — default is shown; prop names the default
<Panel visible={false} />

// GOOD — prop names the exception
<Panel collapsed />
```

## Domain-Accurate Prop Names

- The required / primary value gets the primary name (`price`, `value`, `label`).
- Optional compare-at or secondary values get an explicit secondary name (`originalPrice`), not names that invert meaning (`current` / `after` for a struck-through old value).

## Default Variant Is Idle

- When a component has a `variant` (or status) union, the default should be the initial / idle / earliest state — not an advanced or terminal state.

```ts
// BAD — advanced state as default
variant = 'approved'

// GOOD — initial state as default
variant = 'draft'
```

## Closed Domain Variants

- When a molecule owns a small set of actions or looks, expose a closed union (`'add' | 'edit'`) and map it internally to atoms (e.g. Icon name).
- Do not accept an open foreign enum (every icon name, every button variant) unless the caller is intentionally composing that atom themselves.

```ts
// BAD — caller picks any icon
controlIcon: IconName;

// GOOD — molecule owns the mapping
type ControlVariant = 'add' | 'edit';
```

## Clarity

- Avoid vague names like `data`, `item`, `value`, or `handler` when a more specific name is available.

## Self-Check

- Internal event handlers use `handle...`.
- Callback props use `on...`.
- Boolean names read like booleans and name the exception from the default.
- Prop names match domain meaning (primary + optional secondary).
- Default variant is idle / initial, not advanced.
- Molecule-owned actions use a closed variant union when the caller should not pick glyphs freely.
- Generic names were replaced where a clearer domain name exists.
