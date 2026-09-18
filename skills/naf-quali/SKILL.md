---
name: naf-quali
description: >-
  Builds React UI with the NAF Quali design system (@naf/react-ui,
  @naf/design-tokens). Uses live Storybook at qa-nafdesignsystem.naftech.io
  via storybook-mcp for component APIs, variants, tokens, and screenshots.
  Use when building or refactoring any NAF UI, pages, forms, prototypes,
  dashboards, or when the user mentions Quali, NAF design system, or
  @naf/react-ui.
---

# NAF Quali — build anything with the design system

**Storybook (source of truth):** https://qa-nafdesignsystem.naftech.io/?path=/docs/documentation-introduction--docs

**Packages:** `@naf/react-ui` · `@naf/design-tokens` (Azure Artifacts — not npmjs.org)

## Mandatory workflow (every UI task)

1. **Connect Storybook MCP** (once per session if needed):
   - Namespace: `user-storybook-mcp`
   - `connect({ url: "https://qa-nafdesignsystem.naftech.io/" })`
2. **Find the right primitive** — `search({ query: "<need>" })` or `list({ full: true })`
3. **Read live API** — `get_docs({ path: "<story-id>--docs", format: "markdown" })`
4. **Match a reference screen** (when building a page):
   - Login → `examples-loginform--playground`
   - Account/settings form → `examples-accountform--playground`
   - Generic form → `examples-simpleform--playground`
   - All inputs QA → `design-qa-allinputs--playground`
5. **Screenshot when layout matters** — `screenshot({ path: "<story-id>--playground" })`
6. **Implement** using imports below — props/variants must match Storybook docs exactly
7. **Verify** checklist at bottom before finishing

**Never** invent components, props, variants, or hex colors when a Quali primitive exists.

## Build-any-screen decision tree

| User need | Quali component(s) | Story prefix |
|-----------|-------------------|--------------|
| Page shell / nav | `Top Bar`, `Sidebar`, `Breadcrumb` | `components-navigation-*` |
| Section grouping | `Card`, `Divider` | `components-ui-card`, `components-ui-divider` |
| Primary action | `Button variant="primary"` | `components-ui-button` |
| Secondary / cancel | `Button variant="secondary"` or `tertiary` | `components-ui-button` |
| Delete / irreversible | `Button variant="destructive"` | `components-ui-button` |
| Text link | `Hyperlink` or `Button hyperlink` | `components-ui-hyperlink` |
| Text field | `FormField` + `Input` | `components-forms-formfield`, `components-forms-inputs-input` |
| Password | `FormField` + `PasswordInput` (+ `PasswordHint`) | `components-forms-inputs-passwordinput` |
| Search box | `Search` | `components-forms-inputs-search` |
| Dropdown | `Select` / `MultiSelect` | `components-forms-select`, `components-forms-multiselect` |
| Date / range | `DatePicker` / `DateRangePicker` | `components-forms-datepicker`, `components-forms-daterangepicker` |
| File upload | `FileUpload` | `components-forms-fileupload` |
| Yes/no toggle | `Switch` (Toggle) | `components-forms-switch-toggle` |
| Checkbox / radio group | `Checkbox`, `CheckboxGroup`, `RadioGroup` | `components-forms-*` |
| Tabs | `TabList`, `TabButton`, `TabContext` | `components-navigation-tabs-*` |
| Accordion FAQ | `Accordion` | `components-navigation-accordion` |
| Status chip | `Badge`, `Chip`, `BadgeIcon` | `components-data-display-*` |
| Inline alert | `AlertCard` | `components-ui-alertcard` |
| Toast notification | `useToaster` + `Toast` | `components-notifications-toast` |
| Tooltip | `Tooltip` | `components-data-display-tooltip` |
| Modal dialog | `Modal` | `components-ui-modal` |
| Loading state | `Loader` or `Button isLoading` | `components-ui-loader` |

Full story ID list: [components.md](components.md)

## App setup (required once per project)

**`.npmrc`** (commit registry line only; never commit tokens):

```
registry=https://registry.npmjs.org/
@naf:registry=https://pkgs.dev.azure.com/NAF-Tech/_packaging/NAF-Tech/npm/registry/
always-auth=true
```

Auth: `vsts-npm-auth -config .npmrc` (Windows) or PAT per Storybook Introduction docs.

**Install:**

```bash
npm install @naf/react-ui @naf/design-tokens react react-dom react-hook-form zod @hookform/resolvers
```

**Entry (`main.tsx`) — order matters:**

```tsx
import '@naf/design-tokens/css/light';
import '@naf/react-ui/styles.css';
```

**Provider:**

```tsx
import { DSProvider } from '@naf/react-ui';

<DSProvider theme="light">
  <App />
</DSProvider>
```

- **Light theme only** until official dark guidance ships.
- Peers: React 18+, react-hook-form 7+.

Details: [reference.md](reference.md)

## Import paths

| Category | Import | Examples |
|----------|--------|----------|
| UI | `@naf/react-ui/ui` | `Button`, `Card`, `Modal`, `AlertCard`, `Loader`, `Hyperlink`, `Divider` |
| Forms | `@naf/react-ui/forms` | `FormField`, `Input`, `Select`, `DatePicker`, `PasswordInput`, `FileUpload` |
| Data display | `@naf/react-ui/ui` | `Badge`, `Chip`, `Tooltip` |
| Navigation | `@naf/react-ui/ui` | `TopBar`, `Sidebar`, `Breadcrumb`, `Accordion`, tab primitives |
| Toaster | `@naf/react-ui` | `useToaster` |

Prefer subpath imports for tree-shaking.

## Styling rules

- Custom CSS/SCSS uses **tokens only**: `var(--color-*)`, `var(--spacing-*)`, `var(--radius-*)`
- Common: `var(--color-border-default)`, `var(--color-bg-primary)`, `var(--spacing-400)` … `var(--spacing-700)`
- No raw hex in new code
- Custom `var(--…)` must render under `DSProvider` or with global token import
- Do not replace Quali components with legacy app UI (`components/ui/*`, bespoke SCSS buttons) in **new** work

## Forms pattern

```tsx
import { zodResolver } from '@hookform/resolvers/zod';
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { Button } from '@naf/react-ui/ui';
import { FormField, Input } from '@naf/react-ui/forms';

const schema = z.object({ email: z.string().email() });

function ExampleForm() {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(schema),
  });

  return (
    <form onSubmit={handleSubmit(console.log)}>
      <FormField label="Email" error={errors.email?.message}>
        <Input {...register('email')} />
      </FormField>
      <Button type="submit" variant="primary">Submit</Button>
    </form>
  );
}
```

Always `get_docs` on form components before assuming prop names.

## Page composition pattern

```tsx
import { Card } from '@naf/react-ui/ui';
import { Button } from '@naf/react-ui/ui';

export function ExamplePage() {
  return (
    <main style={{ padding: 'var(--spacing-600)' }}>
      <Card>
        <Card.Header title="Page title" />
        <Card.Body>{/* FormField rows, AlertCard, etc. */}</Card.Body>
        <Card.Footer>
          <Button variant="secondary">Cancel</Button>
          <Button variant="primary">Save</Button>
        </Card.Footer>
      </Card>
    </main>
  );
}
```

Check `components-ui-card--docs` for exact slot/prop names before coding.

## Figma handoff (optional)

Storybook **Ecosystem → Figma MCP** (`ecosystem-figma-mcp--docs`) — use Figma MCP for mockups, then map frames to Quali components via this skill + Storybook docs. Do not copy hex from Figma when a token name exists.

## Anti-patterns

| Do not | Do instead |
|--------|------------|
| `<button className="btn">` | `<Button variant="primary">` |
| `<input type="text">` | `<FormField><Input /></FormField>` |
| `#091644` in CSS | `var(--color-*)` token |
| Guess Button props | `get_docs` on `components-ui-button--docs` |
| Skip Storybook for “simple” UI | Always fetch docs for components used |

## Verify before done

- [ ] Storybook MCP consulted for every Quali component used
- [ ] Token CSS before `@naf/react-ui/styles.css`
- [ ] `DSProvider theme="light"` wraps tree
- [ ] Imports from `@naf/react-ui/ui` or `/forms`
- [ ] No invented props — match live docs
- [ ] Forms use react-hook-form (+ Zod when validating)

## Additional resources

- npm/registry/setup: [reference.md](reference.md)
- Full component + story catalog: [components.md](components.md)
- Screen recipes: [screen-patterns.md](screen-patterns.md)
- Share/install: [README-SHARE.md](README-SHARE.md)
