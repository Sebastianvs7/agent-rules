# Personal coding rules

Canonical files live in this directory. Resolve every relative path from here, not from the tool's home folder.

If a rule in this pack conflicts with the current project, the project wins.

## Always load

- [`00-quality-gates.md`](00-quality-gates.md)

Then load only the topic files that match the task.

## Load by task

- [`01-formatting.md`](01-formatting.md): load when editing JavaScript, TypeScript, JSX, TSX, JSON-like objects, imports, props, arrays, or multiline code.
- [`02-typescript.md`](02-typescript.md): load when editing `.ts` or `.tsx` files, types, interfaces, hooks, helpers, API data, or type errors.
- [`03-react-components.md`](03-react-components.md): load when creating or changing React components, shared UI, repeated markup, dialogs, modals, forms, tables, buttons, list items, heading/title parts, or typography/color via atoms.
- [`04-state-and-callbacks.md`](04-state-and-callbacks.md): load when changing React state, props, event callbacks, parent-child data flow, controlled components, selection/toggle shells, or render props that expose actions.
- [`05-react-composition.md`](05-react-composition.md): load when choosing props-driven vs composition slots vs compound APIs, designing component composition, reusable logic wrappers, function-as-children APIs, render props, flexible UI slots, deciding whether a shell may rewrite slot children from `state`/`variant`, whether layout-only groups may mix items with trailing adornments, string prop vs composition part, whether to extend an existing root vs wrap it, whether a parent may pass `className` into a child to express the parent's state (it may not — the child exposes a closed variant and owns the classes), or whether a new shell may replace an existing compound's parts with loose `ReactNode` props (it may not — compose the compound).
- [`06-naming.md`](06-naming.md): load when adding or renaming handlers, callback props, boolean values, variants, helpers, components, hooks, or variables (including boolean polarity, domain prop names, default variant, closed domain unions).
- [`07-translations.md`](07-translations.md): load when the project has i18n and you are adding, changing, moving, or removing user-facing text.
- [`08-jsx-and-props.md`](08-jsx-and-props.md): load when editing JSX, JSX conditions, props, prop spreading, component render bodies, blank lines between logical blocks, or paired element line breaks.
- [`09-git.md`](09-git.md): load when committing, amending, rebasing/splitting history, creating or updating a PR, merging to main (rebase strategy; one PR then rebase the next), or reviewing a large branch diff.
- [`10-design-system-variants.md`](10-design-system-variants.md): load when the project has a design system (atoms, molecules, `*-variants.ts`), or when editing `*-variants.ts`, composing size/variant Tailwind class maps, deciding what a design-system `index.ts` barrel may export, visual token consistency (border/radius/spacing), whether a DS atom may import another atom’s private helpers (prefer local duplicate for tree-shaking), or whether a caller may pass `className` to a design-system component (temporary escape hatch; a missing look belongs on a prop or variant; story and page layout stays on a wrapper).

## Loading examples

- Fixing a TypeScript helper: load `00-quality-gates.md`, `01-formatting.md`, `02-typescript.md`, and `06-naming.md`.
- Adding a dialog: load `00-quality-gates.md`, `01-formatting.md`, `02-typescript.md`, `03-react-components.md`, `04-state-and-callbacks.md`, `06-naming.md`, `07-translations.md`, and `08-jsx-and-props.md`.
- Adding a reusable design-system atom or molecule: load `00-quality-gates.md`, `01-formatting.md`, `02-typescript.md`, `03-react-components.md`, `05-react-composition.md`, `06-naming.md`, `08-jsx-and-props.md`, and `10-design-system-variants.md` (compose existing atoms via public API; extend root not wrap; no private cross-atom helpers; sort props A–Z with `on*` last; split base vs size class maps; closed domain variants when mapping icons; parent passes a variant such as `offset`, never `className`, into a nested part).
- Renaming an event callback or boolean prop: load `00-quality-gates.md`, `04-state-and-callbacks.md`, and `06-naming.md`.
- Adding UI copy only: load `00-quality-gates.md`, and `07-translations.md` if the project has i18n.
- Preparing a PR / cleaning commit history / landing related PRs: load `00-quality-gates.md` and `09-git.md`.

## Rule priority

1. Follow explicit user instructions for the current task.
2. Follow project-specific conventions and configured tooling.
3. Follow the relevant files in this rule set.
4. If rules conflict, choose the stricter rule unless it breaks the existing project pattern.
