# my-skills

My personal AI agent skills — works with Claude Code, Cursor, Windsurf, Codex, and any agent that reads SKILL.md files.

## Install all skills (global, symlinked to all clients)

```bash
npx skills@latest add <your-username>/my-skills --global
```

## Install a single skill

```bash
npx skills@latest add <your-username>/my-skills --skill cloakbrowser --global
```

## Skills

| Skill | Description |
|---|---|
| [cloakbrowser](./cloakbrowser/SKILL.md) | Stealth Chromium — bypass Cloudflare, reCAPTCHA, FingerprintJS. Drop-in Playwright replacement. |

## Add a new skill

```bash
mkdir my-new-skill
cat > my-new-skill/SKILL.md << 'EOF'
---
name: my-new-skill
description: Use when ...
---

# My New Skill
...
EOF
git add . && git commit -m "feat: add my-new-skill" && git push
```
