# Fixture 01 — Letter decision (write vs skip)

Tests whether the skill correctly drives the "should I write a letter at all?" decision under varied triggering and non-triggering conditions.

Covers: `When to use` section + `Anti-patterns #1` + Reachability test.

Expected pass: **6 / 6**.

## Prompt

`foo-shop` is a project with three active agent sessions in parallel worktrees:

- **orche** — orchestrator on `main`
- **be** — backend implementer in `.worktrees/api-v1`, actively coding right now
- **fe** — frontend implementer in `.worktrees/ui-v1`, actively coding right now

For each scenario, answer: write a letter? (yes/no). If yes, what path. If no, what to do instead. Cite the specific skill rule that drives your answer.

### S1
User chats with `orche`: "API auth should use JWT, not sessions. Tell `be`." `be` is mid-implementation on auth. `fe` doesn't care.

### S2
User chats with `orche`: "Rename the variable `user_id` to `userId` everywhere." Only `be` touches that file, and `be` is actively coding it right now.

### S3
`be` proposes writing `docs/sendbox/toOrche/status-day-N.md` daily so `orche` can check progress at a glance.

### S4
`fe` finds a typo in `be`'s API spec doc — a one-character fix.

### S5
User chats with `orche`: "Auth should use JWT, not sessions." `be` is in a detached worktree and never saw the chat.

### S6
User chats with `orche`: "Rename `user_id` to `userId` everywhere." Only `be` touches that file (detached worktree). The rename will be visible in any commit `be` rebases onto.

## Expected

| # | Answer | Required citation |
|---|---|---|
| S1 | **yes** — write to `docs/sendbox/toBe/from-orche-jwt-auth-decision.md` (single recipient, no frontmatter needed). Substantive directive must reach the detached implementer. | Anti-pattern #1 scope clause OR Reachability test (detached recipient → sendbox is delivery, not redundancy) |
| S2 | **no** — `orche` does NOT write a letter. The rename will be picked up via the commit `be` rebases onto. | Anti-pattern #1 (chat-synchronous decision, will be picked up naturally by commit). Triviality bar from Anti-pattern #1 |
| S3 | **no** — daily status letters are an anti-pattern. State belongs in a dashboard / spec. | Anti-pattern #4 "Using sendbox as state tracking" + Red flag "Letters titled `status-update-N.md` — that's a dashboard" |
| S4 | **no** — open a PR or commit the fix directly. No decision, no blocker, no handoff. | Minimal Sendbox principle (Core Principle #4) + "Do NOT use when ... two sessions can pair-program in real time" |
| S5 | **yes** — same as S1. The decision must reach `be` via sendbox because chat does not reach the detached worktree. | Reachability test (detached recipient is the decisive question) |
| S6 | **no** — even though `be` is detached, the rename is trivial AND will become visible in a rebase. Commit message citing the chat decision is sufficient. | Anti-pattern #1 triviality bar ("skip the letter only when the decision will be picked up naturally by a commit the recipient is about to rebase on") |

## Pass criteria

- Decision (yes/no) matches expected for all 6
- Each "yes" answer cites Reachability test OR Anti-pattern #1 scope clause
- Each "no" answer cites the specific anti-pattern or principle that justifies skipping
- A correct decision with no citation = partial pass; record as a fixture quality issue
