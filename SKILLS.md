# Skills and plugins I use

What I have installed in Claude Code, where each comes from, and how to install it. Nothing here is copied: each links to its source, so you always get the current version under its own license.

Ask before installing anything on a machine that isn't yours. Most of these install for the user, which affects every project on that machine.

## For software work

| Name | What it does | Source | License |
|---|---|---|---|
| **Superpowers** | The core workflow: brainstorming, writing plans, executing plans, test-driven development, systematic debugging, verification before completion, code review, subagent-driven development, git worktrees | [obra/superpowers](https://github.com/obra/superpowers), via the official plugin directory | MIT |
| **VibeSec** | Secure-coding guidance for web apps: injection, XSS, SSRF, auth, secrets | [BehiSecc/VibeSec-Skill](https://github.com/BehiSecc/VibeSec-Skill) | Apache-2.0 |
| **planning-with-files** | File-based planning for long tasks (`task_plan.md`, `findings.md`, `progress.md`) that survives `/clear` | [OthmanAdi/planning-with-files](https://github.com/OthmanAdi/planning-with-files) | MIT |
| **frontend-design** | Production-grade UI that avoids generic AI styling | [anthropics/claude-code](https://github.com/anthropics/claude-code) | see its LICENSE |
| **web-quality-skills** | `accessibility` (WCAG 2.2), `best-practices`, `core-web-vitals`, `performance`, `web-quality-audit` | [addyosmani/web-quality-skills](https://github.com/addyosmani/web-quality-skills) | MIT |

## For other work

| Name | What it does | Source | License |
|---|---|---|---|
| **Cloudflare** | Workers, Pages, D1, KV, R2, Durable Objects, Agents SDK, wrangler | [cloudflare/skills](https://github.com/cloudflare/skills) | Apache-2.0 |
| **Remotion** | Making videos in React | [remotion-dev/claude-code-plugin](https://github.com/remotion-dev/claude-code-plugin) | see repo |
| **claude-seo** | SEO audits: technical, content, schema, local, sitemaps, AI search | [AgriciDaniel/claude-seo](https://github.com/AgriciDaniel/claude-seo) | MIT |
| **humanizer** | Removes AI writing patterns from prose | [blader/humanizer](https://github.com/blader/humanizer) | MIT |

Skills from claude.ai (docx, pdf, pptx, xlsx, skill-creator, deep-research and others) sync automatically when you're signed in to Claude Code with a claude.ai account. Nothing to install.

## Installing

**Plugins**, typed inside a Claude Code session:

```
/plugin install superpowers@claude-plugins-official
```

If that marketplace isn't listed, add it first: `/plugin marketplace add anthropics/claude-plugins-official`. Others:

```
/plugin marketplace add cloudflare/skills
/plugin install cloudflare@cloudflare

/plugin marketplace add remotion-dev/claude-code-plugin
/plugin install remotion@remotion
```

**Skills from GitHub**, in a terminal. The `skills` CLI asks which skills to install and where:

```bash
npx skills add BehiSecc/VibeSec-Skill
npx skills add OthmanAdi/planning-with-files
npx skills add addyosmani/web-quality-skills
npx skills add anthropics/claude-code      # pick frontend-design
```

**humanizer** is a plain clone:

```bash
git clone https://github.com/blader/humanizer.git ~/.claude/skills/humanizer
```

**claude-seo** has its own installer. Follow its README.

Start a new session afterwards and type `/` to check the skills appear.

## If I can only have one

Superpowers. Everything in [skills/ticket/SKILL.md](skills/ticket/SKILL.md) uses its debugging, test-first and verification skills when they're there, and falls back to the same discipline when they're not.
