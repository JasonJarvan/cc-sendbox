# Fixture 03 — Cross-cwd write (where the letter goes)

Tests Core Principle #0 (canonical sendbox / main agent) and the Cross-cwd write 3-case table, plus broadcast lifecycle field shape.

Covers: Core Principle #0 / Cross-cwd write / Red flags (fan-out detection).

Expected pass: **6 / 6**.

## Prompt

A multi-agent project lives at `/home/dev/myapp` (the orchestrator / main agent's cwd). Two subagents were dispatched:

- **sub-A**: cwd = `/home/dev/myapp/.worktrees/feature-x` (same repo, different worktree on branch `feature/x`)
- **sub-B**: cwd = `/home/dev/refactor-utils` (a **completely separate repo** the subagent was sent to read/edit, e.g. an external library copy)

### S1
sub-A finished its slice and writes a `milestone-done` letter back to the orchestrator. Where should it write? Give a path; cite the rule.

### S2
sub-B finished its slice and needs to send the same `milestone-done` letter back to the orchestrator. Where should it write? Give an absolute path; cite the rule.

### S3
sub-B notices `/home/dev/refactor-utils/` has no `docs/sendbox/` folder. Should it run `mkdir -p /home/dev/refactor-utils/docs/sendbox/toOrchestrator` and write there? yes/no + cite rule.

### S4
In your own words, what is the "main agent" per this skill?

### S5
sub-A is in a worktree. The orchestrator on `main` is waiting for sub-A's `milestone-done` so the merge can happen. The letter must be visible **before** the worktree merges. Should sub-A (a) commit the letter on its worktree branch and let it ride the merge, or (b) write directly to the main worktree path? Cite the default-channel rule.

### S6
The orchestrator wants to broadcast "ruff lint mandatory pre-commit, all sessions install it" via `toAllActiveSessions/from-orche-ruff-precommit.md`. What concrete shape should the `lifecycle:` field in the frontmatter take? Give one example string.

## Expected

| # | Answer | Required citation |
|---|---|---|
| S1 | `/home/dev/myapp/docs/sendbox/toOrchestrator/from-sub-a-<milestone>-done.md` (or commit on the worktree branch so it rides with the merge) | Cross-cwd write table case 1 (`<project>/.worktrees/<task>/`); Default channel rule: milestone-done rides the merge → commit on worktree is acceptable; visible-before-merge → write to main directly |
| S2 | `/home/dev/myapp/docs/sendbox/toOrchestrator/from-sub-b-<milestone>-done.md` (absolute path to main agent's sendbox) | Cross-cwd table case 2 ("Completely different repo … Write to the main agent's sendbox by absolute path … Do NOT create docs/sendbox/ under your own cwd") |
| S3 | **No.** Do not create a shadow sendbox. If unreachable, surface to user instead. | Core Principle #0 ("Never create a parallel docs/sendbox/ under a side cwd — sendboxes do not fan out") AND Hard rules "If the subagent cannot reach the main agent's path … it MUST surface that to the user rather than write a shadow sendbox" |
| S4 | The main agent is the session whose cwd is the project root (typically the orchestrator on main branch / main worktree), and its `docs/sendbox/` is the single canonical source of truth for all letters. | Core Principle #0 definition |
| S5 | **Write directly to the main worktree path.** Pre-merge visibility requires it; commit-on-worktree is the default only when the letter rides with the merge. | Default preference rule in Cross-cwd write |
| S6 | A concrete evaluable condition — e.g. `until ruff config committed to main` OR `until superseded by toAllActiveSessions/from-orche-<next>.md`. Open-ended "until done" is rejected. | YAML frontmatter spec: "`lifecycle` should be a concrete condition a future reader can evaluate without context (a date, a milestone name, a 'superseded by' pointer). Avoid open-ended 'until done'." |

## Pass criteria

- S1 accepts either commit-on-worktree OR write-to-main (both valid per the case-1 row; the rule cites both options)
- S2 must specify **absolute** path. Relative-from-sub-B path is wrong (sub-B is in a different repo, has no path traversal to main).
- S3 must say **no** with explicit no-shadow / no-fan-out rationale
- S4 definition must mention (a) cwd = project root AND (b) holds canonical `docs/sendbox/`
- S5 must say "write to main" with rationale tied to pre-merge visibility
- S6 must include a concrete trigger (date / milestone / supersession pointer) — generic "until done" fails this scenario
