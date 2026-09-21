# TypeScript Rules

Load when editing `.ts` or `.tsx` files, types, interfaces, hooks, helpers, API data, or type errors.

## Explicit Types

- In TypeScript files, every exported function, component, hook, helper, object, and public value must have an explicit type.
- Function parameters must always be typed.
- Component props must always be typed with a named type or interface.
- Hook return values should be explicitly typed when they are reused outside the hook.
- Avoid implicit `any`; do not rely on TypeScript guessing unclear types.
- Let TypeScript infer obvious local values when the type is immediately clear, such as `const count = 0`, unless explicit typing improves readability or public API safety.

## Equality

- Always use strict comparison: `===` and `!==`.
- Never use `==` or `!=`, including the `!= null` / `== null` idiom.
- Check `null` and `undefined` separately (`value !== null && value !== undefined`), or use `??` / optional chaining when that is the intent.

## Member Order

- Sort type and interface members alphabetically by name.
- Put callback / handler members (`on*`) last, still alphabetically among themselves.
- JSX prop order follows the same rule — load [`08-jsx-and-props.md`](08-jsx-and-props.md).

```ts
// BAD
export interface PanelProps {
  padding?: PanelPadding;
  badge?: React.ReactNode;
  onExpandedChange?: (expanded: boolean) => void;
  className?: string;
}

// GOOD
export interface PanelProps {
  badge?: React.ReactNode;
  className?: string;
  padding?: PanelPadding;
  onExpandedChange?: (expanded: boolean) => void;
}
```

## Type Assertions

- Avoid `as` type assertions whenever possible.
- Prefer proper typing, generics, type guards, discriminated unions, `satisfies`, or narrowing instead of forcing a type with `as`.
- Use `as` only at trusted boundaries where TypeScript cannot infer the type correctly, such as third-party APIs, DOM APIs, parsed external data after validation, or test mocks.
- Never use `as any` unless there is no reasonable alternative, and leave a short explanation when it is unavoidable.
- Prefer making invalid states unrepresentable with precise types instead of fixing type errors with assertions.

## Self-Check

- Public TypeScript values have explicit types.
- Parameters are typed.
- Props use a named type or interface.
- Comparisons use `===` / `!==`, never `==` / `!=`.
- Type members are alphabetical, with `on*` handlers last.
- No avoidable `as` assertions remain.
- No unexplained `as any` remains.
