# Claude Code workflow

How I work with Claude Code on a production codebase: a change protocol with review stops, hard rules for the agent, a codebase-mapping prompt and a few quick prompts.

The idea behind all of it: the agent writes most of the code, and I direct, review, test and decide what ships. It works in phases, stops for review, separates what it verified from what it assumed, and doesn't call anything done without evidence.

## What's here

| File | What it is |
|---|---|
| [CLAUDE.local.md](CLAUDE.local.md) | Working rules and hard rules. Loaded in every session in the repo. |
| [skills/ticket/SKILL.md](skills/ticket/SKILL.md) | `/ticket`: the change protocol. Understand → investigate → plan → implement → verify → self-review, with a low-risk lane for small changes. |
| [skills/ticket/impact-checklist.md](skills/ticket/impact-checklist.md) | Blast-radius checklist used in the plan and again on the final diff. |
| [codebase-mapping.md](codebase-mapping.md) | Prompt for mapping an unfamiliar repo with parallel subagents, plus a quick mode. |
| [PROMPTS.md](PROMPTS.md) | Quick prompts: orientation, architecture, environment problems, proving a test, review before commit, steering lines. |

## Using it in a repo

Ask the repo's owner before adding anything. These files stay local: they're excluded through `.git/info/exclude`, so they never show up in `git status` or a commit.

macOS / Linux / Git Bash, from the root of the target repo:

```bash
git clone https://github.com/AustinH5544/claude-code-workflow.git ~/claude-code-workflow

# Don't overwrite anything the repo already has
for f in CLAUDE.local.md .claude/skills/ticket; do [ -e "$f" ] && echo "STOP: $f already exists"; done

cp ~/claude-code-workflow/CLAUDE.local.md .
mkdir -p .claude/skills
cp -R ~/claude-code-workflow/skills/ticket .claude/skills/

printf '\n/CLAUDE.local.md\n/.claude/skills/ticket/\n/codebase-map/\n' >> .git/info/exclude
git status   # should show no new files
```

Then start a new Claude Code session in the repo and check that `/ticket` appears when you type `/`.

No install allowed? Open [PROMPTS.md](PROMPTS.md) in the browser and paste.

To remove: `rm -rf CLAUDE.local.md .claude/skills/ticket codebase-map` and delete the three lines from `.git/info/exclude`.
