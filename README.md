# Claude Code workflow

How I work with Claude Code on a production codebase: a change protocol with review stops, hard rules for the agent, a codebase-mapping prompt, a database design checklist and a few quick prompts.

The idea behind all of it: the agent writes most of the code, and I direct, review, test and decide what ships. It works in phases, stops for review, separates what it verified from what it assumed, and doesn't call anything done without evidence.

## What's here

| File | What it is |
|---|---|
| [PROMPTS.md](PROMPTS.md) | Start here. First 10 minutes in a new repo, then quick prompts: orientation, architecture, environment problems, proving a test, review before commit, steering lines. |
| [CLAUDE.local.md](CLAUDE.local.md) | Working rules and hard rules. Loaded in every session in the repo. |
| [skills/ticket/SKILL.md](skills/ticket/SKILL.md) | `/ticket`: the change protocol. Understand → investigate → plan → implement → verify → self-review, with a low-risk lane for small changes. |
| [skills/ticket/impact-checklist.md](skills/ticket/impact-checklist.md) | Blast-radius checklist used in the plan and again on the final diff. |
| [codebase-mapping.md](codebase-mapping.md) | Prompt for mapping an unfamiliar repo with parallel subagents, plus a quick mode. |
| [database-design.md](database-design.md) | Schema design and review checklist, a prompt that runs it, and refreshers on the principles. |
| [SKILLS.md](SKILLS.md) | The skills and plugins I use, with sources and install commands. |

## Option A: install in one repo

Ask the repo's owner before adding anything. These files stay local: they're listed in the repo's local exclude file, so they never show up in `git status` or a commit.

macOS / Linux / Git Bash, from the root of the target repo:

```bash
git clone https://github.com/AustinH5544/claude-code-workflow.git ~/claude-code-workflow

# Don't overwrite anything the repo already has
for f in CLAUDE.local.md .claude/skills/ticket; do [ -e "$f" ] && echo "STOP: $f already exists"; done

cp ~/claude-code-workflow/CLAUDE.local.md .
mkdir -p .claude/skills
cp -R ~/claude-code-workflow/skills/ticket .claude/skills/

# Works in a normal clone and in a worktree
printf '\n/CLAUDE.local.md\n/.claude/skills/ticket/\n/codebase-map/\n' >> "$(git rev-parse --git-common-dir)/info/exclude"
git status   # should show no new files
```

Then start a new Claude Code session in the repo and check that `/ticket` appears when you type `/`.

To remove: `rm -rf CLAUDE.local.md .claude/skills/ticket codebase-map` and delete the three lines from the exclude file.

## Worktrees (Paradev, Conductor, `git worktree`)

Tools that run each task in its own git worktree only copy **tracked** files into it. These files are untracked on purpose, so **a new worktree won't have them, and `/ticket` won't appear.** The exclude lines are shared across worktrees, so only the copy step needs repeating.

Either run this from inside each new worktree:

```bash
cp ~/claude-code-workflow/CLAUDE.local.md . && mkdir -p .claude/skills && cp -R ~/claude-code-workflow/skills/ticket .claude/skills/
```

or use Option B. Either way, check that `/ticket` appears in the first session.

## Option B: install for the user (every repo and worktree)

Ask first, since this changes the machine's Claude Code setup for every project. Remove it when you're done.

```bash
# Don't overwrite an existing setup
for f in ~/.claude/CLAUDE.md ~/.claude/skills/ticket; do [ -e "$f" ] && echo "STOP: $f already exists"; done

mkdir -p ~/.claude/skills
cp -R ~/claude-code-workflow/skills/ticket ~/.claude/skills/
cp ~/claude-code-workflow/CLAUDE.local.md ~/.claude/CLAUDE.md
```

If `~/.claude/CLAUDE.md` already exists, don't overwrite or edit it. Use Option A, or paste the rules as the first message of each session.

To remove: `rm -rf ~/.claude/skills/ticket ~/.claude/CLAUDE.md` (only if you created them).

## Codex and other agents

Codex reads `AGENTS.md`, not `CLAUDE.local.md`, and won't see `/ticket`. Without changing any of the repo's files:

1. Paste [CLAUDE.local.md](CLAUDE.local.md) as the first message.
2. Paste the protocol from [skills/ticket/SKILL.md](skills/ticket/SKILL.md), from "# Production change protocol" down, with the ticket text in place of `$ARGUMENTS`. Paste the [impact checklist](skills/ticket/impact-checklist.md) when it reaches Phase 3.

The same works for any agent, and for Claude Code when installing isn't allowed.
