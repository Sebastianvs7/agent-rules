# Design System Variant Classes

Load when the project has a design system (atoms, molecules, `*-variants.ts`). Use this file when creating or editing `*-variants.ts` files, composing Tailwind class maps for design-system atoms/molecules, deciding barrel exports, visual token consistency (border/radius/spacing), or whether one DS atom may import another’s private helpers.

## Base vs Variant Maps

- Put classes that apply for **every** size/state of an element into a `*BaseClasses` constant.
- Put into a `*SizeClasses` / `*VariantClasses` `Record` **only** the classes that actually differ by that key.
- Do not duplicate shared layout classes (e.g. `shrink-0`, `flex`, `items-center`) inside every map entry.
- Compose in JSX with the project's class merger (`clsx`, `cn`, `classNames`, …): `cx(base, sizeMap[size], …)`.

```ts
// BAD — shared layout duplicated in every size entry
export const iconSizeClasses: Record<Size, string> = {
  default: '!size-6 shrink-0',
  sm: '!size-4 shrink-0',
};

// GOOD — base holds shared; size map holds only size diffs
export const iconBaseClasses = 'shrink-0';

export const iconSizeClasses: Record<Size, string> = {
  default: '!size-6',
  sm: '!size-4',
};
```

```tsx
className={cx(
  iconBaseClasses,
  iconSizeClasses[size],
)}
```

## Naming

- Prefer `{element}BaseClasses` + `{element}SizeClasses` (or `{element}{Axis}Classes` for other axes).
- Keep map names honest: if it is named `*SizeClasses`, it must not carry non-size concerns.

## Barrel / Public API

`index.ts` is the design-system public contract. Export only what callers are meant to import.

- **Do export:** components, compound parts, and public types that callers need (public unions / size enums).
- **Do not export:** Tailwind class constants from `*-variants.ts` (`*Classes`, `*ClassMaps`). Those are implementation details for files inside the same folder.
- Prefer selective type re-exports over `export * from './…-variants'`.

```ts
// BAD — leaks internal class strings as DS public API
export * from './widget';
export * from './widget-variants';

// GOOD — components (+ types only when callers need them)
export * from './widget';
export type { WidgetSize } from './widget-variants'; // only if public
```

Heuristic: if a symbol is only imported by sibling files in the same molecule/atom folder, it does not belong in the barrel.

## Visual Token Consistency

- Reuse the same border width, radius, and spacing conventions as the rest of the design system unless the design explicitly specifies otherwise.
- Prefer the shared default (typically `1px` borders) over one-off thicker/thinner strokes that only appear in a single molecule.

## Atom Independence (Helpers & Tree-Shaking)

- An atom/molecule folder is a self-contained unit. Do **not** import another atom’s private helpers, `*-variants` class constants, or non-public utils (e.g. `../atom-a/get-fill-percent`).
- Prefer **duplicating** a small pure helper inside the consumer folder over sharing it across sibling atoms. A few duplicated lines beat a cross-atom dependency that pulls an unrelated component into the import graph.
- Compose via the **public component API** (`import { Icon } from '../icon'`) when you need another atom’s UI — not via its internals.
- Extract a shared util only when it is a real shared primitive (tokens, typography helpers, …) with multiple stable callers — not because two PRs happen to need the same clamp math.

```ts
// BAD — atom-b depends on atom-a internals
import { getFillPercent } from '../atom-a/get-fill-percent';
import { atomAFillClasses } from '../atom-a/atom-a-variants';

// GOOD — keep the helper (and track/fill classes) local to atom-b/
import { getFillPercent } from './get-fill-percent';
import { atomBFillClasses } from './atom-b-variants';
```

## Self-Check

- No shared class is repeated across every entry of a size/variant map.
- Size maps contain only size-differing utilities.
- Component composes base + map via the project's class merger.
- Barrel does not re-export `*Classes` / variant class maps; public types are exported selectively when needed.
- Borders/radius/spacing match DS conventions unless design says otherwise.
- No private cross-atom imports (helpers / `*-variants` class constants); small helpers are local when needed.
