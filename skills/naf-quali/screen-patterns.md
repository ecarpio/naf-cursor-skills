# NAF Quali — screen patterns

Use these recipes with the main [SKILL.md](SKILL.md) workflow. Always `get_docs` + `screenshot` on cited stories before coding.

## Login / auth

**Reference:** `examples-loginform--playground`

```
TopBar (optional) → Card or centered column
  FormField + Input (email)
  FormField + PasswordInput
  Button primary fullWidth "Sign in"
  Hyperlink or Button hyperlink "Forgot password"
AlertCard for errors (not raw red text)
```

## Registration

**Reference:** `examples-accountform--playground`, `design-qa-allinputs--playground`

```
Card.Header with title
Stack of FormField rows (name, email read-only if prefilled, phone, password)
PasswordHint under password field
Button primary submit
Consent copy as typography + Hyperlink links
```

## Settings / profile form

**Reference:** `examples-accountform--playground`

```
Card with sections separated by Divider
FormField grid (two columns only if Storybook example shows it)
Button secondary Cancel + primary Save in Card.Footer
Toast on success via useToaster
```

## List + detail / dashboard shell

**Reference:** `components-navigation-sidebar--playground`, `components-navigation-top-bar--playground`

```
Sidebar (nav items) + main column
TopBar (title, user menu)
Breadcrumb under TopBar
Card sections for widgets
Badge / Chip for status
Loader while fetching
```

## Confirmation / destructive action

**Reference:** `components-ui-modal--playground`

```
Modal with short copy
Button tertiary Cancel + Button destructive Confirm
Optional AlertCard variant warning inside Modal body
```

## Empty / error states

```
AlertCard for page-level messages
Loader centered in Card.Body while loading
Button primary for retry CTA
```

## Search + filter bar

**Reference:** `components-forms-inputs-search--playground`

```
Search input (wide)
Select / MultiSelect for filters inline
Button secondary "Clear filters"
```

## Multi-step / tabs

**Reference:** `components-navigation-tabs-tablist--playground`, `components-navigation-tabs-tabcontext--playground`

```
TabList + TabButton for steps
TabContext panels with form sections
Button primary "Continue" / secondary "Back" in footer
```
