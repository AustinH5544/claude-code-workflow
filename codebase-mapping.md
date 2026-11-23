# Codebase mapping prompt

Use at the start of work on an unfamiliar repo. The agent writes a map of the codebase that later sessions read instead of re-exploring. For a short time box, use **Quick mode** at the bottom.

Why it is written this way, from running it on a large Next.js / Prisma monorepo:
- "Map only, no bug hunting" is stated up front, because the subagents started reporting bugs.
- The main session settles contradictions between subagents itself, from the code or the shell.
- Mismatches between docs and code get recorded, not acted on.
- The environment check is read-only, because the wrong runtime version was what blocked work later.
- Reading the instruction files is explicitly step 1.

---

> Fill in the `{{…}}` placeholders. Delete any line that doesn't apply.

```
I'm about to work on bug tickets (and possibly a feature) in this repository:
{{REPO_PATH}}. Before any of that, I want a deep, written understanding of
how the codebase works, recorded so that later sessions can reuse it
instead of re-exploring.

## Ground rules
- READ-ONLY for everything that already exists. Don't change, format,
  install, migrate, build, start servers or run tests. Run only commands
  that read: git log/status/ls-files/check-ignore, version checks,
  listing files.
- The ONLY thing you may create is a new folder, `codebase-map/`, at the
  repo root (or {{OUTPUT_PATH}}).
- MAPPING ONLY. I already have the bug tickets, so don't hunt for bugs,
  propose fixes or refactor. When something surprises you, record it as a
  "gotcha" (non-obvious behaviour to keep in mind when changing this code),
  not as a bug report.
- Off limits: {{e.g. "don't touch the payments folder" / none}}.
- Mark claims as **verified (how)** or **assumption**. Cite `path:line`
  for concrete claims. Treat line numbers as approximate pointers.
- If the docs and the code disagree, record it under "doc vs code" and
  say which one the code supports. Don't fix either.

## Steps

1. **Read the instructions first.** Read every instruction and orientation
   file: README*, CLAUDE.md / CLAUDE.local.md, AGENTS.md (at any depth),
   CONTRIBUTING*, .cursor/.github instructions, project skills or commands
   (e.g. .claude/skills/, .agents/), and any architecture docs. Summarise
   the rules a contributor must follow. Tell me you've done this.

2. **Quick survey (main session, keep it light).** Look at the top-level
   tree, the workspace and package manifests, the language/framework
   versions, the CI config, and how the app starts. Check the local
   environment read-only: is the required runtime version installed, is
   the package manager on PATH, are there OS assumptions (e.g. POSIX-only
   scripts on Windows)? Report anything that will block running the
   project later.

3. **Propose the split. This is the one checkpoint.** Split the codebase
   into {{5 to 7}} areas of roughly equal weight, e.g. instructions and
   tooling/CI; database/schema; platform/auth/shared libs; the core
   domain feature(s); secondary features/integrations; testing/UI/i18n.
   Show me the split in a short table (area → folders) and wait for my
   go-ahead.

4. **Map in parallel.** Start one subagent per area, so the main context
   stays small. Give each the ground rules above, its folders, the
   rules summary from step 1, and this output template for its file:
   - Purpose of the area, in a few lines
   - Key files table (path → what it does)
   - Main entities/types and how they relate
   - Lifecycles / request flows, step by step, with `path:line`
   - Entry points (routes, handlers, jobs, CLI) and what calls what
   - Authorization / tenancy / config forks that apply here
   - How to run and test this area (commands, env vars, ports)
   - Gotchas (non-obvious behaviour; NOT bug reports)
   - Doc-vs-code mismatches
   - Open questions / not verified
   Each subagent writes `codebase-map/NN-<area>.md` and returns a short
   summary plus any claims it's unsure about.

5. **Reconcile (main session).** Read the summaries. Where subagents
   contradict each other or are unsure, settle it yourself from the code
   or a read-only shell command, and correct the files. Check that the
   files don't overlap or contradict each other.

6. **Write `codebase-map/README.md`** (aim for under ~100 lines):
   - How a new session should use the map: read the instruction files,
     then this README, then only the area file for the ticket
   - A table of the files and what each covers
   - "The system in one page": product, architecture/layering,
     data/tenancy, auth, key cross-cutting concerns
   - "Where to look for…": a table mapping common needs to starting files
   - Local environment warnings (from step 2)
   - Doc-vs-code mismatches across all areas
   - Audit date, and a note that line numbers drift

7. **Hand off.** Tell me:
   - what was produced (files and line counts);
   - which claims are still assumptions;
   - which surprises are worth my attention.
   Then offer, without doing either:
   (a) adding `/codebase-map/` to `.git/info/exclude`;
   (b) adding a line to the local agent instructions so future sessions
       read the map first.
   If you have persistent memory, save a pointer to the map's README.

## Quick mode (use if I say we're time-boxed to {{N}} minutes)
Do steps 1 and 2. Skip the checkpoint. Map only these {{2 or 3}} areas, the ones
most relevant to {{TICKET / FEATURE AREA}}, and write a README of at most
40 lines. List what you skipped.
```
