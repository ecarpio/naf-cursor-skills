# Apply Quali — SCSS override recipes

Companion to `apply-quali/SKILL.md`. Use when Quali looks “unstyled”, oversized, or crushed by hub CSS.

## Why Quali loses

1. **Cascade layers** — Quali ships in `@layer naf-design-system`. Unlayered app CSS always wins over layered Quali rules.
2. **Universal resets** — `* { padding: 0; margin: 0; box-sizing: border-box; }` after (or unlayered vs) Quali zeros control padding.
3. **Rem root** — `html { font-size: 62.5%; }` makes `1rem = 10px`; old hub chrome used `5.2rem` fields. Quali assumes a normal root → wrong if you restyle in rem against 62.5%.
4. **Double chrome** — Adapter renders Quali but old `button.scss` / `input.scss` still targets inner elements.

## Fix A — layer order (once per app)

Create a tiny CSS file imported **before** `@naf/react-ui/styles.css`:

```css
/*
 * Declare cascade layer order BEFORE @naf/react-ui/styles.css loads.
 * Quali components use @layer naf-design-system — hub reset must stay below.
 */
@layer naf-hub-reset, naf-design-system;
```

Put any unavoidable hub reset inside `@layer naf-hub-reset { ... }` so Quali wins.

Entry order:

```ts
import '@naf/design-tokens/css/light';
import './styles/quali-layer-order.css'; // or equivalent
import '@naf/react-ui/styles.css';
```

## Fix B — kill padding-zeroing resets

Search project for:

- `* {` with `padding: 0` / `margin: 0`
- `input, button, select, textarea {` global resets
- `button { all: unset; }` / aggressive normalize after Quali

Remove padding/margin zeroing from those rules, or move them into `@layer naf-hub-reset` and stop targeting Quali internals.

## Fix C — gut file checklist

For each migrated control (`Button`, `Input`, `Select`, `Tooltip`, …):

1. Open paired `*.scss`.
2. Delete selectors that style the control chrome.
3. Leave layout wrappers (`.quali-*__row`, gaps, max-width).
4. Grep for the old class names; remove dead imports from parents.
5. Do not keep “temporary” `!important` bridges — gut instead.

## Fix D — token bridge (legacy pages only)

When a page still has large legacy SCSS but must look Quali-adjacent:

- Map colors/spacing to `var(--…)` in a bridge file.
- Do **not** reintroduce field chrome; only page shell / typography / gaps.
- Prefer migrating the control to Quali over bridging forever.

## Rem strategy

| Goal | Approach |
|------|----------|
| Quali default sizing | `html { font-size: 100%; }` (16px) |
| Keep 62.5% temporarily | Do not write new rem chrome; use Quali `size` props only |
| Mixed codebase | Quali controls: no rem chrome in SCSS. Legacy islands: isolate until migrated |

## Tooltip / overlay note

Legacy always-visible yellow tooltips are not Quali. Replace with Quali `Tooltip`
(`size="md"` default, map `up`→`top`, `down`→`bottom`). Coach marks that must stay
open: Quali `open` prop + content as `ReactNode` — still no legacy bubble SCSS.

## Quick grep list

```text
padding:\s*0
5\.2rem|52px|min-height:\s*5
\.btn|button\.scss|input\.scss
tooltip\.scss|yellow
!important
```

Any hit near a Quali adapter → gut or move to layout-only tokens.
