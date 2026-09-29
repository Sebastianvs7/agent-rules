# React Component Rules

Load when creating or changing React components, shared UI, repeated markup, dialogs, modals, forms, tables, buttons, or list items.

## Rules

- When designing a reusable component API, choose props-driven vs composition slots vs compound first — load [`05-react-composition.md`](05-react-composition.md). If a compound already covers the layout, compose its parts. Do not replace them with `ReactNode` props.
- Do not create multiple components that render the same purpose or duplicate the same markup.
- Use one shared component for repeated UI patterns such as dialogs, modals, forms, tables, buttons, and list items.
- For dialogs, use a single reusable dialog component/configuration pattern instead of rendering many different dialog components with duplicated structure.
- When the project has a design system or component library, compose existing primitives instead of reimplementing their markup, styles, or behavior. Look there before adding new ones; wrap, slot, or nest what is already there.
- Prefer specializing an existing root component (override `className` / `data-testid` / variant) over wrapping it in an extra `div` — details in [`05-react-composition.md`](05-react-composition.md).
- Keep components focused on rendering and orchestration.
- Move complex state transformations, filtering, mapping, validation, and formatting into helpers or hooks when it improves clarity.
- Do not pass React state setters directly to child components.
- Do not pass `className` into a child to apply a look that follows the parent's state. The child owns that look behind a closed prop (`offset`, `size`). Details: [`05-react-composition.md`](05-react-composition.md).

## Typography And Color

- Named title / heading parts should render as semantic heading elements (`h1`–`h6`), not generic boxes, unless the part is purely decorative.
- When the project has typography or color primitives, use those instead of one-off utility colors. If a needed variant is missing, extend that public API rather than hardcoding a one-off.

```tsx
// BAD — raw color utility + non-semantic title
<span className="text-slate-600">Secondary copy</span>
<div className="font-medium">Section title</div>

// GOOD — semantic heading; color via the project's text primitive when it has one
<h3 className={titleClasses}>Section title</h3>
```

## Domain Variants

When a component owns a closed set of actions or looks, expose a domain union and map it internally to primitives (icon, button, …). Do not leak an open enum (e.g. every icon name) unless the caller is meant to compose the glyph themselves — details in [`06-naming.md`](06-naming.md).

## Self-Check

- Reusable component API model was chosen via `05-react-composition.md` when designing a new shared component.
- New UI reuses existing design-system or library primitives instead of duplicating them.
- Specializations use the existing component as root, not an outer wrapper only for test id or width.
- Title parts use semantic headings when appropriate; color/typography go through project primitives when they exist.
- Domain actions use a closed variant union when the caller should not pick glyphs freely.
- Repeated markup has been consolidated into a shared component when appropriate.
- Dialogs/modals use one shared pattern.
- Complex logic is not buried inside JSX.
- Children receive values and semantic callbacks, not raw setters.
- No parent passes `className` into a child to encode the parent's state. The child maps its own variant to classes.
