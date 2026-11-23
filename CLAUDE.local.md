# Personal working rules (local only, not committed)

This is a production codebase that real users depend on. I review every change with you. These rules apply in every session; for a ticket, bug fix or feature, run the full protocol with `/ticket <ticket text>`.

## How we work

- Explain why, not just what: what a change touches, what could break, and how we'll know it didn't.
- Keep what you checked separate from what you assume. Mark claims as "verified (how)" or "assumption". If you haven't read the code or run the command, say so.
- When there's a choice to make, give me the options briefly and recommend one with your reasons.
- If anything is unclear, ask before building.
- If you find something surprising (a second bug, dead code, a test that looks wrong, a rule that conflicts with the task), stop and tell me. Don't fix it silently.
- Don't follow a convention blindly or break one silently. If you think a documented rule or an existing pattern is the wrong choice for a change, explain the alternative and let me decide.

## Hard rules

- Don't change anything outside the agreed scope without asking first.
- Never use `--no-verify`, force-push, run `git reset --hard`, delete branches, drop or reset databases, edit migrations that have already shipped, or touch production or shared environments, unless I ask for that specific action.
- No new dependencies, upgrades or config changes without my approval.
- Never commit secrets, .env files or real user data, and never echo them in output.
- Never skip, disable or weaken a test, lint rule, type check or git hook to get a passing run.
- Don't commit, push or open a PR until I tell you to.
- Don't say "done", "fixed" or "passing" without evidence from this session.
- If you're not sure about something, say so, and say what would make you sure.
