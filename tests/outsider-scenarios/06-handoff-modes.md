# Fixture 06 — Handoff modes (child vs inheritance)

Tests whether the skill correctly drives `handoff.md` mode selection (child vs inheritance) and rejects the peer-dialogue case as not a handoff.

Covers: `SKILL.md` Handoff modes section + Mode detection rule.

Expected pass: **4 / 4**.

## Prompt

For each scenario, identify the handoff mode (child / inheritance / not-a-handoff) and list 3 specific content requirements the letter MUST include (or MUST exclude) per the skill rules. Cite which Mode A/B rule justifies your answer.

### S1
Orchestrator on `main` is spawning a fresh implementer in `.worktrees/payment-refactor` to refactor the payment module. The orchestrator continues running, coordinating other implementers in parallel, and the implementer is expected to report back with a `milestone-done` letter when the refactor is complete.

### S2
The current orchestrator session has been running for 4 hours and the context window is filling. The user is starting a new "successor" session to continue the orchestrator's role for the rest of the v0.2 sprint. The current session will end after writing the entry letter; the successor will own everything from then on.

### S3
Two existing implementer sessions (`impl-A` on backend, `impl-B` on frontend) need to coordinate on API contract details. `impl-A` wants to send `impl-B` the agreed-on payload schema so `impl-B` can start mock-implementing the UI. Neither owns the other.

### S4
The orchestrator is spawning a fresh subagent to do a 30-minute one-off task: scan all `.tsx` files for a deprecated `useEffect` pattern and report counts. The subagent dies after reporting. The orchestrator continues.

## Expected

| # | Mode | Required content (must include AT LEAST these) | Forbidden content | Citation |
|---|---|---|---|---|
| S1 | **child-handoff (Mode A)** | (1) Subtask scope: payment module refactor scope + success criteria; (2) Convergence path: `from-impl-payment-refactor-milestone-done.md` to orchestrator's `to<Orche>/`; (3) Parent's cwd absolute path (because subagent is in `.worktrees/`) | Full project context dump; reading lists beyond payment module; unrelated pending decisions | Mode A required content table; Mode detection rule (parent persists + child reports back) |
| S2 | **inheritance-handoff (Mode B)** | (1) Identity statement ("you are successor orchestrator, you inherit responsibility for v0.2 sprint coordination"); (2) Full status snapshot table (done / in-flight / pending); (3) Must-read list (version-plan, recent letters, active blockers, architecture); plus pending decisions, active relationships, env state | Convergence path back to parent (parent will be gone); subtask-scoped framing | Mode B required content table; Mode detection rule (parent ends + no report back) |
| S3 | **NOT a handoff** | n/a — use a peer letter type. The right letter is `from-impl-a-<topic>.md` to `toImplB/` (e.g. `from-impl-a-api-payload-schema.md`) using normal letter types (decisions / milestone-done / blocker as fits), not a handoff letter | Inventing a peer-handoff variant | Mode C rule "Peer dialogue — not a handoff" |
| S4 | **child-handoff (Mode A)** | (1) Subtask scope: scan .tsx files for deprecated useEffect pattern, deliver counts; (2) Convergence path: `from-<subagent>-milestone-done.md` with the counts; (3) Out-of-scope statement: do NOT modify any files | Full project context; long reading list (the task is read-only counting) | Mode A required content table; ultra-short subtask reinforces "minimum context" rule |

## Pass criteria

- S1 mode = child; must mention convergence path AND minimal context (no full dump)
- S2 mode = inheritance; must mention identity statement AND full status snapshot AND NO convergence path
- S3 = NOT a handoff; subagent should explicitly reject the framing and suggest a peer letter type instead
- S4 mode = child; must mention minimal scope + out-of-scope statement; full project dump for a 30-min counting task is a hard fail
- Mode confused (e.g. calling S1 inheritance) = hard fail
- Forbidden content correctly flagged in at least 2 of S1, S2, S4 = full pass; flagged in only 1 = partial
