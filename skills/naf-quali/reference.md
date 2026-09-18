# NAF Design System — Integration Reference

Source: Storybook integration docs at https://qa-nafdesignsystem.naftech.io/?path=/docs/documentation-integration--docs

## Registry (`.npmrc`)

Place in same directory as app `package.json`:

```
@naf:registry=https://pkgs.dev.azure.com/NAF-Tech/_packaging/NAF-Tech/npm/registry/
registry=https://registry.npmjs.org/
```

Authenticate:

```bash
npx vsts-npm-auth -config .npmrc
```

## Install

```bash
npm install @naf/react-ui @naf/design-tokens
```

Pin `@naf/design-tokens` to the version your UI package depends on. Resolve peer dependency warnings from npm.

## Requirements

- `react` / `react-dom`: `>=18.0.0 <21.0.0`
- `react-hook-form`: `>=7.0.0` (form APIs)
- Modern bundler with npm `exports` support (Vite, Webpack 5+, Rspack)

## Global styles

```tsx
// main.tsx — order matters
import '@naf/design-tokens/css/light';
import '@naf/react-ui/styles.css';
```

Only use `exports`-documented paths from `@naf/design-tokens`. If resolution fails, check `node_modules/@naf/design-tokens/package.json`.

## Tokens in custom CSS

```css
.my-panel {
  border: 1px solid var(--color-border-default);
  padding: var(--spacing-400);
}
```

Prefer tokens over raw hex. Tokens are CSS custom properties generated from the design system source (Supernova).

## Forms pattern

```tsx
import { zodResolver } from '@hookform/resolvers/zod';
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { Button } from '@naf/react-ui/ui';
import { FormField, Input } from '@naf/react-ui/forms';

const schema = z.object({
  email: z.string().email(),
});

function MyForm() {
  const { register, handleSubmit, formState: { errors } } = useForm({
    resolver: zodResolver(schema),
  });

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <FormField label="Email" error={errors.email?.message}>
        <Input {...register('email')} />
      </FormField>
      <Button type="submit" variant="primary">Submit</Button>
    </form>
  );
}
```

Always call `get_docs` on the relevant form component story before assuming prop names.

## Verification checklist

- `@naf/design-tokens/css/light` resolves without bundler errors
- `@naf/react-ui/styles.css` loads after tokens
- `DSProvider` wraps the component tree
- DevTools show CSS variables under `.naf-design-system` / themed root
