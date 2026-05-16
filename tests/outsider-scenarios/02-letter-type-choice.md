# Fixture 02 — Letter type choice

Tests whether the skill drives correct selection from the 10-type letter catalog given a triggering condition.

Covers: Letter type catalog + Communication patterns (A-12 stop-and-ask, bundle ack, broadcast).

Expected pass: **5 / 5**.

## Prompt

`foo-shop` has three active sessions: `orche` (orchestrator on `main`), `be` (backend in `.worktrees/api-v1`), `fe` (frontend in `.worktrees/ui-v1`).

For each scenario decide: (1) write a letter? yes (assume yes for all in this fixture), (2) full path, (3) does it need YAML frontmatter? (4) which letter type from the catalog? (5) 1-3 sentence content shape.

### S1
`be` is spawning for the first time. The repo has no sendbox folder yet. `orche` needs to give `be` its full entry context (repo status, what's done, what to build next, must-read files).

### S2
`be` finished the API spec for v1 and wants `fe` to start coding against it. `fe` is on a separate worktree.

### S3
`be` discovered the OpenAPI spec the user agreed to last week conflicts with a constraint from a third-party payment provider that `be` only just learned about. `be` cannot merge as-is; it needs `orche` (or escalated to user) to decide between three approaches: (a) ask provider for exception, (b) change the spec, (c) defer the feature.

### S4
Over an hour, `be` sent `orche` 5 small clarifying questions (auth-token TTL, error envelope shape, pagination style, rate-limit headers, idempotency-key requirement). `orche` has answers to all 5 and needs to respond.

### S5
A new linter (`ruff`) just got installed by `orche` and now ALL sessions must run it pre-commit. Both `be` and `fe` need to know.

## Expected

| # | Path | Frontmatter? | Letter type | Content shape |
|---|---|---|---|---|
| S1 | `docs/sendbox/toBe/handoff.md` | No (single recipient; `> from / recipient / purpose / lifecycle` quote block at top is sufficient) | `handoff.md` (entry letter) | Identity, repo status snapshot, must-reads, day-1 actions, lifecycle-end disposition |
| S2 | `docs/sendbox/toFe/from-be-api-v1-plan-ready.md` | No (single recipient) | `from-<x>-plan-ready.md` | Step checklist, path to API spec, key decisions summary, items needing fe ack, risk signals |
| S3 | `docs/sendbox/toOrche/from-be-blocker-payment-spec-conflict.md` | No (single recipient) | `from-<x>-blocker-<topic>.md` (A-12 stop-and-ask) | 2-sentence TL;DR, timeline w/ commit hashes, mismatch core, options table (a/b/c) with work/risk/time, be's tentative pick, current snapshot. STOP, do not merge |
| S4 | `docs/sendbox/toBe/from-orche-be-decisions.md` | No (single recipient) | `from-<orche>-<x>-decisions.md` (Multi-decision ack bundle) | Numbered `### D1 … D5`, each with answer + action item. Single bundled letter, not five |
| S5 | `docs/sendbox/toAllAgents/from-orche-ruff-precommit.md` (or `toAllActiveSessions/`) | **YES** — multi recipient | `toAllActiveSessions/from-<orche>.md` (Broadcast) | Per-item heading, scope, action required, supersession rule. Lifecycle string is concrete (e.g. "until ruff config committed to main" or "until superseded by ...") |

## Pass criteria

- Path includes `to<Role>/` PascalCase or accepted short alias (subagent may use `toBe`/`toFe`/`toOrche` — that is valid per Naming rules table "Short role aliases")
- Letter type name matches catalog row
- Frontmatter answer is correct for single vs multi recipient
- For S3, content shape must mention an **options table** AND mention of stopping (A-12 stop-and-ask discipline)
- For S4, must explicitly say "bundle, not 5 letters" or equivalent (anti-pattern #7 awareness)
- For S5, multi-recipient frontmatter is non-negotiable — single-recipient answer fails this scenario
