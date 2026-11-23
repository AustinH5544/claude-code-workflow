# Quick prompts

Short prompts for the moments the `/ticket` skill doesn't cover. Copy, fill in the `{{…}}`, paste.

---

## 0. First 10 minutes in a new repo

**Ask the team before starting:**
- Which branch do I work from, and do you want one branch per ticket?
- Is the environment ready (runtime versions, database, env files), or is there a setup step?
- Am I allowed to add skills or local instruction files? Any tools I shouldn't use?
- Does the repo have a CLAUDE.md or AGENTS.md you rely on?
- What format do you want for design work: a doc in the repo, a ticket breakdown, a diagram?
- How do you want me to check in: after each ticket, or only when I'm stuck?

**Check yourself (read-only):**
```bash
git status && git branch --show-current && git log --oneline -10
ls -a                       # README, CLAUDE.md, AGENTS.md, .claude/, docs/
cat package.json | head -40 # engines, packageManager, scripts (or the equivalent manifest)
node -v                     # or the runtime this repo uses
```

**Then** set up the workflow files if allowed (see the README), and run orientation (1) or the [codebase map](codebase-mapping.md).

---

## 1. Quick orientation (time-boxed)

For when there's no time for the full [codebase map](codebase-mapping.md).

```
Read-only. Don't change, install, build or run anything.
Use a subagent to explore this repo and write a summary to
{{../notes/codebase-summary.md}}, outside the repo. Cover:
- what the product does and who uses it
- the stack and versions, and how the app starts
- the folder layout
- how a page gets its data, traced end to end with file paths
- where tests live and how to run them
- the rules in CLAUDE.md / AGENTS.md / README that apply to contributors
- anything surprising
Cite a file path for every claim. Mark anything you didn't verify as an
assumption. Keep it under 150 lines. Time box: {{10}} minutes.
```

---

## 2. Run a ticket

With the skill installed:

```
/ticket {{paste the ticket text}}
```

Without it (skills not allowed): paste the contents of [skills/ticket/SKILL.md](skills/ticket/SKILL.md) from "# Production change protocol" down, with the ticket text in place of `$ARGUMENTS`, and paste [CLAUDE.local.md](CLAUDE.local.md) first.

---

## 3. Architect a feature (no code)

```
Design this feature. Don't write code. Write the design to
{{docs/<feature>.md}}.

Feature: {{description}}

Read the instruction files and the closest existing feature first, and
design to fit how this codebase already works. Cover:
1. Questions I'd ask the product owner first, and the assumptions you made
2. Data model: tables, columns, types, keys, indexes, and how it fits the
   existing models. Why each index exists.
3. API changes: endpoints or procedures, inputs, outputs, errors
4. UI: screens and states (loading, empty, error)
5. Edge cases: time zones, retries and duplicates, partial failure,
   editing and deleting
6. Permissions, tenancy and security: who can do what, checked where
7. Rollout: migration, feature flag, order of deploys, how to back out
8. An ordered list of small, testable tickets
9. Test plan
Mark every assumption. Keep it to what a reviewer can read in 10 minutes.
```

Then check the data model against [database-design.md](database-design.md).

---

## 4. Something broke that isn't my code

For a failed hook, a missing command, the wrong runtime version, a database that's down.

```
Stop. Don't fix anything yet and don't change anything global.
1. Show me the exact error.
2. Tell me whether it's our code or the environment, with evidence.
3. Check the project's docs and manifests (README, package.json engines
   and packageManager, setup scripts, .nvmrc, docker files) for the
   intended setup.
4. Give me the simplest fix that changes nothing outside this repo, or
   tell me what to ask the team.
Never skip hooks or checks (--no-verify, HUSKY=0) to get past it.
```

---

## 5. Prove the test catches the bug

```
Show me the new test fails without the fix: put the original code back
temporarily, run only that test, show the failure, then restore the fix
and show it passing. Confirm with git diff that the file is back to the
fixed version.
```

---

## 6. Review before commit

```
Before committing:
- Show git status and the full staged diff.
- Flag anything unrelated to the ticket (formatting, other files, stray
  changes) and leave it unstaged.
- Run the checks that apply (type check, lint, the tests for this area)
  and paste the real results.
- Draft a commit message in this repo's format.
Don't commit until I say so. Stage files by name, never git add -A.
```

---

## 7. Steering lines

Short corrections for mid-session.

- `We're time-boxed to {{N}} minutes. Combine Phases 1 to 3 into one report, then implement without stopping between steps. Phases 5 and 6 still run in full.`
- `Simplest fix that changes nothing global, please.`
- `You're over-investigating. Give me your best answer now and what you're unsure about.`
- `Show me the evidence: the command and its output.`
- `Is that verified or an assumption?`
- `That's out of scope. Note it as a follow-up and leave it alone.`
- `Stop and explain the plan before writing any more code.`
- `What would a strict reviewer flag in this diff?`
