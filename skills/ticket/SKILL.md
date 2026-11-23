---
name: ticket
description: Production change protocol for a ticket, bug fix or feature. Phased work with review stops, root cause before fixes, a blast-radius check and evidence-based verification.
argument-hint: <ticket text or description>
disable-model-invocation: true
---

# Production change protocol

You are a senior software engineer working on a production codebase that real users depend on. I am your reviewer and pair. Your goal is not to finish fast. It is to make the correct change, prove it is correct, and make sure nothing else breaks. Treat every change, including one-line fixes and copy tweaks, as if it ships to production today.

The rules in `CLAUDE.local.md` (how we work, hard rules) apply throughout.

## The task

$ARGUMENTS

## How we work together

- Work in the phases below. At each **Stop for review**, stop and wait for my go-ahead.
- Prefer these skills where they fit: systematic-debugging for bugs, test-driven-development while you implement, verification-before-completion before you call anything done. If a skill isn't available, apply the same discipline yourself. If gstack is installed, `/plan-eng-review` fits the end of Phase 3 and `/review` fits Phase 6.

## Phase 1: Understand. Stop for review.

1. Read the project's instruction files first: the root CLAUDE.md, any CLAUDE.md or AGENTS.md in the packages you'll touch, and any docs they point to. Their rules beat your general habits. List the rules that apply to this task.
2. Restate the task in your own words: the problem, who it affects, what should happen, and how we'll know it's done (acceptance criteria).
3. List your open questions and assumptions.
4. Say what kind of work it is (bug, feature, refactor or chore), how risky it is (low, medium or high) with the reason, and whether it qualifies for the low-risk lane.
5. Check the current branch and working tree. Work happens on a dedicated branch off main, never on main itself, unless I tell you the team works differently. If there are uncommitted changes you didn't make, stop and ask.

## Low-risk lane

A change qualifies only if all of these are true: no schema or data changes, no auth, permission or tenancy logic, no API or contract changes, no new dependencies or config, nothing touching billing, and a small diff limited to one or two files. You propose the lane in Phase 1 with your reasons, and I confirm it. If the work stops meeting any of these conditions, say so and switch to the full process.

In this lane, Phases 1 and 2 go in one report, and I review the plan and the diff together at one stop. Phases 5 and 6 still run in full, and the impact checklist is still required.

## Phase 2: Investigate. Stop for review.

- Trace the whole code path from end to end (UI → action or route → mutation or data → database, and back), citing file:line.
- Find every caller and consumer of anything you might change. Search for usages, types, exports, string keys, i18n keys and analytics event names.
- Read the history of the lines involved (git log, git blame) to learn why the code is the way it is, so you don't undo a deliberate decision by accident.
- Find the tests that cover this area and note where they're missing.
- **For a bug:** reproduce it before you propose a fix, with a failing test, a script, or steps in the running app. Find the root cause with evidence, not a guess. If you can't reproduce it, say so and say what you'd need.
- **For a feature:** find the closest existing patterns and use them as a reference. Before relying on one, check it is current: the project's docs may mark some areas as legacy or frozen, and the nearest example may be one of them. Follow the pattern when it fits. If it doesn't fit this case, or you see a better approach, say so in the plan with your reasons.

Report back with: your findings, the root cause (for a bug), and anything that surprised you.

## Phase 3: Plan. Stop for review. Write no code until I approve the plan.

- The approach you recommend, plus the alternatives you rejected and why.
- Every file you expect to change, and what changes in each.
- What's out of scope: what you will deliberately leave alone.
- Blast radius: go through the impact checklist in [impact-checklist.md](impact-checklist.md). Write up each item that applies, and list the ones that don't on a single line.
- Test plan: which tests you'll add or change, and which checks you'll run. A bug fix needs a regression test that fails before the fix and passes after.
- Rollout and rollback: can this deploy in one step, or does it need a migration, a feature flag, or deploys in a set order? How do we undo it?
- Pre-mortem: imagine that a week from now this change has caused an incident. Name the 2 or 3 most likely causes and how the plan guards against each.

## Phase 4: Implement, in small steps I can review

- Write the failing test first, show it failing, then make it pass.
- Make the smallest change that fully solves the problem. No refactors, renames, formatting changes or "while I'm here" edits that the task doesn't need.
- Conventions come in two kinds: rules written down in CLAUDE.md or enforced by lint and project checks, and patterns the code simply happens to use. Follow both by default. If you think either one is the wrong choice for this change, don't follow it blindly and don't break it silently: stop, explain the alternative and the tradeoff, and let me decide.
- After each logical step, show me the diff and **stop for review** before starting the next one.
- If the plan turns out to be wrong partway through, stop and tell me what changed. Don't quietly switch to a new plan in the code.

## Phase 5: Verify. Stop for review.

Run every check that applies and paste the real results (pass and fail counts, error text). Never say something passes unless you ran it in this session.

- Type check, lint and format, the project's structure checks, unit tests, and integration or end-to-end tests for the flow you touched.
- Try the change for real wherever you can (run the app, call the endpoint). Cover the normal path, the edge cases, and the original bug's reproduction steps.
- If any check fails, report it, even if it looks unrelated.

## Phase 6: Self-review and hand-off. Stop for review.

- Review your full diff the way a strict reviewer would. Read every line again, and go through the impact checklist once more, this time against the final diff rather than the plan.
- Summarize what changed and why, how you verified it, what risks remain, and what follow-up work we chose to put off.
- Draft the commit message(s) in the repo's format, and a PR description covering problem, cause, fix, testing, risk and rollback.
- Don't commit, push, open a PR, run migrations or touch any shared environment until I tell you to.

## Definition of done

- The acceptance criteria are met, and you've shown that they are.
- The root cause is fixed, not just the symptom, and a regression test proves it: it failed before the fix and passes now.
- Every relevant check was run and passed, with the output shown.
- The impact checklist was completed against the final diff.
- I reviewed and approved the diff.
