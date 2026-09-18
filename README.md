# NAF Cursor skills (Quali)

Shared Cursor Agent skills for the NAF Quali design system. Clone once; install into `~/.cursor/skills/` (or symlink).

| Skill | When to use |
|-------|-------------|
| [`skills/naf-quali`](skills/naf-quali/) | Build or refactor UI with `@naf/react-ui` + Storybook MCP |
| [`skills/apply-quali`](skills/apply-quali/) | Override legacy SCSS; force Quali chrome on migrations |

**Storybook:** https://qa-nafdesignsystem.naftech.io/?path=/docs/documentation-introduction--docs

## Install skills

```bash
git clone <this-repo-url> naf-cursor-skills
cd naf-cursor-skills
```

### Windows (PowerShell)

```powershell
$skills = "$env:USERPROFILE\.cursor\skills"
New-Item -ItemType Directory -Force -Path $skills | Out-Null
Copy-Item -Recurse -Force .\skills\naf-quali "$skills\naf-quali"
Copy-Item -Recurse -Force .\skills\apply-quali "$skills\apply-quali"
```

### macOS / Linux

```bash
mkdir -p ~/.cursor/skills
cp -R skills/naf-quali ~/.cursor/skills/
cp -R skills/apply-quali ~/.cursor/skills/
```

### Symlink (pull updates without recopy)

```powershell
# Windows (Admin or Developer Mode)
New-Item -ItemType Junction -Path "$env:USERPROFILE\.cursor\skills\naf-quali" -Target (Resolve-Path .\skills\naf-quali)
New-Item -ItemType Junction -Path "$env:USERPROFILE\.cursor\skills\apply-quali" -Target (Resolve-Path .\skills\apply-quali)
```

```bash
# macOS / Linux
ln -s "$(pwd)/skills/naf-quali" ~/.cursor/skills/naf-quali
ln -s "$(pwd)/skills/apply-quali" ~/.cursor/skills/apply-quali
```

Reload Cursor after install.

## Storybook MCP (required for `naf-quali`)

Add to `~/.cursor/mcp.json` (merge with existing servers):

```json
{
  "mcpServers": {
    "storybook-mcp": {
      "command": "npx",
      "args": ["-y", "@raksbisht/storybook-mcp"],
      "env": {
        "STORYBOOK_URL": "https://qa-nafdesignsystem.naftech.io/"
      }
    }
  }
}
```

Reload Cursor.

## Invoke

- `/naf-quali` — build with Quali + Storybook
- `/apply-quali` — migrate / kill legacy SCSS fighting Quali
- Or mention Quali, NAF design system, `@naf/react-ui`, “apply quali”

## Update

```bash
cd naf-cursor-skills
git pull
# if you copied (not symlinked), re-run the Copy-Item / cp steps
```

## Later

Same folders can move into a NAF Cursor Marketplace plugin without rewrite.
