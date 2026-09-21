# React Composition Rules

Load when designing component composition, choosing a reusable component API model (props-driven vs slots vs compound), reusable logic wrappers, function-as-children APIs, render props, flexible UI slots, deciding whether a shell may rewrite slot children from `state`/`variant`, whether layout-only groups may mix items with trailing adornments, or whether to extend an existing root vs wrap it.

## Choose An API Model First

Before implementing a reusable component, pick one model:

| Model | When | Props may control |
| --- | --- | --- |
| **Props-driven** | Leaf UI; everything concerns only this component | Own look/behavior only (`size`, `disabled`, `variant`, `invalid`) |
| **Composition (slots)** | Inner parts must be swappable (loader, trigger, empty, control) | Layout slots (`ReactNode` / `children`); caller owns nested markup |
| **Compound** | Fixed related parts share state/context (Root + Parts) | Root orchestration; each part owns its own props |

Default for design-system primitives with swappable inner UI: **composition or compound**, not props-driven configuration of nested components.

## Props-Driven

OK: props that affect the component itself (`disabled`, `size`, `invalid`).

Wrong: props that exist only to configure a nested component (`loading` + `spinnerType` → render `<Spinner type={spinnerType} />` inside). That is prop drilling into another domain and inverts control — the caller cannot own nested UI without extending the shell API.

```tsx
// BAD — shell owns nested Spinner config
const Shell = ({
  loading,
  spinnerType,
}: {
  loading: boolean;
  spinnerType: string;
}) => (
  <div>
    {loading && <Spinner type={spinnerType} />}
  </div>
);

<Shell loading={true} spinnerType="circle" />
```

## Composition (Slots)

- Use slot props (`loader`, `control`, `children`) typed as `ReactNode`.
- When the nested UI already exists in the design system, pass or render that atom/molecule in the slot — do not reimplement it in the shell.
- The shell decides **where** a slot renders, not **how** it looks.
- Caller decides visibility and nested props (`loader={isLoading ? <Spinner size="sm" /> : null}`), not boolean + type props on the shell.

```tsx
// GOOD — shell places the slot; caller owns Spinner
const Shell = ({
  loader,
}: {
  loader?: React.ReactNode;
}) => (
  <div className="flex">
    {loader !== null && loader !== undefined && <span>{loader}</span>}
  </div>
);

<Shell loader={loading ? <Spinner type="circle" /> : null} />
```

## String Prop vs Composition Part

When text or a titled region sits in the **same layout place** as other children and the shell does not need to place it elsewhere, do not split it into a string prop (`title`, `label`, …) beside `children`. Prefer `children` or a named compound part.

Use a dedicated string prop only when the shell must position that content differently from the rest (e.g. sticky header vs body).

```tsx
// BAD — title and body are adjacent; shell does not distinguish layout
<Panel title="Settings">
  <Body />
</Panel>

// GOOD — compound parts (or plain children) own the content
<Panel>
  <PanelTitle>Settings</PanelTitle>
  <PanelBody />
</Panel>
```

## Do Not Rewrite Slot Content

When a slot is typed as `ReactNode` (`index`, `label`, `icon`, `control`, …), the shell must render it as-is. Do not replace, wrap-swap, or invent nested UI from another shell prop.

Shell props may style the shell’s own chrome (e.g. `state` → circle color, `variant` → background). They must not pick the glyph, text, or child component inside a slot.

```tsx
// BAD — shell rewrites the index slot from state
const indicator = state === 'done' ? <Icon name="tick" /> : index;
<div className={chromeClasses[state]}>{indicator}</div>

// GOOD — shell styles chrome only; caller owns the glyph
<div className={chromeClasses[state]}>{index}</div>

<Step
  index={(
    <StepIndex>
      <Icon name="tick" />
    </StepIndex>
  )}
  label={<StepLabel>Details</StepLabel>}
  state="done"
/>

<Step
  index={<StepIndex>2</StepIndex>}
  label={<StepLabel>Review</StepLabel>}
  state="current"
/>
```

Same idea: a `variant` prop may map colors only — it must not auto-pick an icon; the caller passes `icon` when needed.

## Compound Components

- Root plus named parts (`Root` + `RootOptions` / `RootOption`).
- Shared state via context; parts keep their own props.
- Use when structure is fixed and parts need the same parent state — not when a single slot is enough.

## Extend Root, Do Not Wrap

When specializing an existing atom/molecule, make that atom the **root** of the new component. Pass through or override `className`, `data-testid`, and variant props.

Do not add an outer wrapper `div` only to host a test id or force `w-full` — that nests duplicate roots and breaks layout.

```tsx
// BAD — extra wrapper around an existing root
<div data-testid="design-system-specialized" className="w-full">
  <Base data-testid="design-system-base">{children}</Base>
</div>

// GOOD — specialize by using Base as root
<Base className={className} data-testid="design-system-specialized">
  {children}
</Base>
```

## Layout-Only Lists Keep Homogeneous Children

When a group/list wrapper is **layout-only** (spacing between like items — e.g. `flex` + `gap`), its `children` must be only those items.

Do not put trailing adornments (error, hint, footer) inside the same flex/gap container. They share the item gap and break the spacing contract. Render them as **siblings** outside the group; their top spacing (`mt-*`) is the contract between group and adornment.

```tsx
// BAD — error gets the same gap as items
<ItemGroup>
  <Item>…</Item>
  <Item>…</Item>
  <ItemGroupError>Please select an option.</ItemGroupError>
</ItemGroup>

// GOOD — group spaces items only; error owns its own offset
<ItemGroup>
  <Item>…</Item>
  <Item>…</Item>
</ItemGroup>
<ItemGroupError>Please select an option.</ItemGroupError>
```

Prefer a dedicated adornment component (`ItemGroupError`) over an `errorMessage` prop on the layout-only group.

## Render Props

- Use function-as-children / render props when a child needs parent-owned state, computed values, or actions without low-level implementation details.
- Prefer render props for reusable logic components where the caller controls markup.
- Keep render-prop APIs semantic and small.
- Do not expose raw setters through render props; expose intent-based callbacks.
- Use normal `children` when the child does not need state/actions from the parent.
- Do not use function-as-children just to avoid creating a component; use it when it improves composition.

```tsx
<FormState>
  {({ values, errors, onValuesChange }) => (
    <UserForm
      values={values}
      errors={errors}
      onValuesChange={onValuesChange}
    />
  )}
</FormState>
```

## Self-Check

- An API model was chosen before implementation (props-driven, slots, or compound).
- No props exist only to configure nested UI (`*Type`, boolean+loader pairs that belong to a child).
- Adjacent title/label content uses `children` or a compound part when the shell does not place it elsewhere.
- Slots or compound parts are used instead of drilling nested config through the shell.
- Slot `ReactNode` props are rendered as-is — shell does not rewrite them from `state` / `variant` / similar.
- Specializations use the existing atom as root — no wrapper `div` only for test id or width.
- Layout-only list/group children are homogeneous — errors/hints sit as siblings with their own `mt-*`, not inside the item gap.
- Nested UI reuses existing design-system atoms/molecules when they already exist.
- Render props are used only when they improve composition.
- Render props expose semantic callbacks, not setters.
- Normal `children` is used when no parent state/actions need to be shared.
