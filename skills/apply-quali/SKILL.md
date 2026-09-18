---
name: apply-quali
description: >-
  Forces Quali (@naf/react-ui, @naf/design-tokens) over legacy SCSS/CSS and
  bespoke UI chrome on any project. Use when the user says apply quali, override
  old SCSS, kill legacy styles, migrate components off rem/hex chrome, fix Quali
  padding lost to hub resets, or when legacy .scss fights Quali components.
---

# Apply Quali — override legacy SCSS

**Authority:** This skill **overrides** any project habit that keeps or extends
legacy button/input/select/tooltip SCSS when a Quali primitive exists.

**Companion:** Always load and follow `naf-quali` first (Storybook MCP, imports,
tokens, `DSProvider`). This skill adds the **migration / override** rules.

**Storybook:** https://qa-nafdesignsystem.naftech.io/

## Non-negotiables

1. **Quali owns chrome.** Border, padding, height, font-size, focus ring, colors
   on fields/buttons/tooltips/modals → Quali component + tokens. Not app SCSS.
2. **Do not restyle Quali.** No wrapping class that resets `input`/`button` /
   `[class*="Input"]` padding, border, or font to match old rem chrome.
3. **Gut legacy field SCSS.** When adapting a control to Quali, delete (or empty
   to layout-only) the old `*.scss` rules for that chrome. Leaving both = Quali
   loses to later/unlayered hub CSS.
4. **Tokens only** in remaining custom CSS: `var(--color-*)`, `var(--spacing-*)`,
   `var(--font-*)`, `var(--border-radius-*)`, `var(--radius-*)`. No new hex.
5. **Default size `md`** unless Storybook docs say otherwise for that control.
6. **Never invent props** — `get_docs` via Storybook MCP before coding.

## Mandatory workflow

Copy and track:

```
Apply Quali:
- [ ] 1. Load naf-quali; connect Storybook MCP
- [ ] 2. Map UI need → Quali primitive (decision tree in naf-quali)
- [ ] 3. get_docs for that component
- [ ] 4. Replace or wrap legacy component with Quali
- [ ] 5. Gut competing SCSS (chrome out; layout-only in)
- [ ] 6. Fix cascade (layer order + kill universal resets)
- [ ] 7. Verify checklist below
```

### Step 4 — replace vs adapter

| Situation | Do |
|-----------|----|
| New screen / new control | Import Quali directly (`@naf/react-ui/ui` or `/forms`) |
| Wide legacy API (`components/ui/Button`) | Adapter: keep export, render Quali inside; map props via size/variant maps |
| One-off page still on raw `<input>` / `.btn` | Replace with Quali in that file; do not add more legacy SCSS |

### Step 5 — gut SCSS (hard rule)

**Delete or stop shipping** rules that set any of these on Quali-owned elements:

- `padding`, `height` / `min-height`, `border`, `border-radius` (field chrome)
- `font-size`, `line-height`, `font-family` on inputs/buttons (use Quali / tokens)
- Rem-based “52px field” / `5.2rem` control chrome
- Absolute yellow tooltip bubbles when Quali `Tooltip` exists
- `appearance` hacks that fight native Quali controls

**Keep only layout** in app SCSS (prefer tokens):

- Flex/grid, `gap`, `width` / `max-width`, margin between sections
- Positioning for page shells (sidebar offset, page padding) using tokens

Comment at top of gutted files:

```scss
/* Layout only — Quali owns field/button chrome. */
```

### Step 6 — cascade wins Quali

Legacy unlayered CSS beats Quali `@layer` styles. Fix foundation once per app:

1. **Import order** (entry):
   ```ts
   import '@naf/design-tokens/css/light';
   // optional: layer-order CSS that declares @layer before Quali
   import '@naf/react-ui/styles.css';
   ```
2. **Declare layers before Quali CSS** (example):
   ```css
   @layer naf-hub-reset, naf-design-system;
   ```
3. **Remove or narrow** universal resets that zero Quali padding, e.g.
   `* { padding: 0; margin: 0; }` — especially unlayered and loaded after Quali.
4. Prefer `html { font-size: 100%; }` (16px rem root) so Quali rem sizing is correct.
   Do not keep `html { font-size: 62.5%; }` while relying on Quali defaults.

Details: [scss-override.md](scss-override.md)

## Component override map (legacy → Quali)

Use this when ripping SCSS off old wrappers:

| Legacy pattern | Quali |
|----------------|-------|
| `.btn` / custom Button SCSS | `Button` (`variant`, `size`) |
| text `<input>` + label SCSS | `FormField` + `Input` |
| password field | `FormField` + `PasswordInput` (+ `PasswordHint`) |
| select / dropdown SCSS | `FormField` + `Select` |
| checkbox / radio SCSS | `Checkbox` / `RadioGroup` |
| textarea SCSS | `FormField` + `Textarea` |
| currency / masked money | `MaskedInput` / `NumberInput` as documented |
| date picker chrome | Quali `DatePicker` (or documented date input) |
| toast / banner SCSS | `useToaster` / `AlertCard` |
| modal / popup SCSS | `Modal` |
| spinner overlay SCSS | `Loader` |
| tooltip yellow bubble SCSS | `Tooltip` (`size` sm\|md\|lg, default `md`) |
| badge / chip SCSS | `Badge` / `Chip` |

If not listed → Storybook `search`, then `get_docs`. Do not invent a third CSS kit.

## Anti-patterns (forbidden under this skill)

| Forbidden | Required |
|-----------|----------|
| “Match old rem look” with SCSS on Quali nodes | Accept Quali `sm`/`md`/`lg` |
| `!important` to beat Quali | Gut hub rule or fix layer order |
| Duplicate chrome: Quali + legacy `.scss` both active | Gut legacy chrome |
| New hex / magic rem for fields | Tokens + Quali sizes |
| Skip Storybook because “only CSS tweak” | Still `get_docs` if touching a Quali control |
| Extend `components/ui/*.scss` for new features | Quali component or layout-only tokens |

## Verify before done

- [ ] `naf-quali` workflow done (Storybook docs for every Quali component touched)
- [ ] Token CSS before `@naf/react-ui/styles.css`; `DSProvider theme="light"` present
- [ ] Legacy chrome SCSS removed or layout-only for migrated controls
- [ ] No universal padding reset killing Quali
- [ ] Layer order declared if hub uses `@layer` / Quali still loses specificity wars
- [ ] Visual check: field padding/height matches Storybook `md` (or chosen size), not old 5.2rem

## Additional resources

- Cascade / rem / gut recipes: [scss-override.md](scss-override.md)
- Full Quali build skill: personal `naf-quali` (`SKILL.md`, `components.md`, `reference.md`)
