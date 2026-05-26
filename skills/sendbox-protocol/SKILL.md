---
name: sendbox-protocol
version: 0.2.1
description: Use when multiple agents/sessions need asynchronous file-based communication across worktrees, branches, or sessions — defines directory layout, letter naming, frontmatter spec, lifecycle (burn/archive/persist), and the canonical letter types (handoff, plan-ready, greenlight, blocker, decisions, milestone-done, broadcast). Apply when designing or running multi-agent orchestration (one orchestrator + multiple implementers / subagents) and chat-synchronous coordination is insufficient.
---

# Sendbox Protocol

A file-based, lifecycle-managed **letter** convention for asynchronous coordination between agent sessions. Letters live in version control alongside code, so handoffs survive session reset, branch switch, and worktree isolation. Battle-tested on a 4-day sprint with 11+ agents, 22+ letter types, and 8 sendbox directories.

## When to use

**Use when:**
- One orchestrator session coordinates 2+ implementer sessions (separate worktrees or git branches)
- An agent must pause and request human or upstream decision before proceeding (stop-and-ask)
- Decisions, blockers, or milestones must outlive the current chat session
- A subagent finishes work in an isolated worktree and needs to hand artifacts back upstream
- Broadcast to all active sessions (e.g. tool-chain change, new constraint)

**Do NOT use when:**
- The user gave a clear, trivial decision in chat AND no upstream agent has follow-up work AND no other session needs to know → just commit. See [Minimal Sendbox](#anti-patterns).
- The information belongs in durable docs (architecture, spec, memory) → use those instead.
- Two sessions can pair-program in real time → just talk.

**Reachability test (the decisive question):** if the recipient is in a detached worktree / separate session, chat does NOT reach them — sendbox is the *delivery* mechanism, not redundancy. The "skip the letter" exemption only applies when the recipient will naturally pick the decision up (they share the chat, OR the decision becomes visible in a commit they'll rebase onto before it matters).

## Core principles

0. **One canonical sendbox per project, held by the main agent.** The *main agent* is the session whose working directory is the project root — typically the orchestrator on the main branch / main worktree. Its `docs/sendbox/` is the **single source of truth**. Every other session (subagents in `.worktrees/<task>/`, subagents in a completely different repo or cwd, ephemeral helpers in `/tmp`) writes letters *to the main agent's sendbox by absolute path*. Never create a parallel `docs/sendbox/` under a side cwd — sendboxes do not fan out.
1. **Roles over sessions.** Address letters to a *role* (`toOrchestrator/`, `toIpcAuthor/`), not a session name. Sessions die; roles persist across handovers.
2. **Letters are transient.** Every letter has a defined lifecycle and must be burned (`git rm`), archived, or promoted to durable docs at lifecycle end. Rotting unburned letters is the #1 hygiene failure.
3. **Single recipient = no metadata; multi recipient = mandatory metadata.** The frontmatter trade-off matches the coordination cost.
4. **Minimal sendbox.** If a decision is synchronous, trivial, and self-contained, do not write a letter. Commit message citing the chat decision is sufficient audit.
5. **Sendbox is communication, not state.** Project state belongs in a dashboard or spec; letters merely *trigger* state changes.
6. **ASCII / English filenames.** No localized characters, no spaces, no special chars — letters cross worktrees and OSes.
7. **Letters are append-only within a lifecycle.** Edit only to mark stale or link to a superseding letter; do not silently rewrite a letter another agent has read.

## Directory layout

```
docs/sendbox/                       # repo-root convention
  to<Role>/                         # one dir per recipient role
    handoff.md                      # session-entry brief (single recipient → no frontmatter required)
    from-<sender>-<topic>.md        # letters back to that role
  toAllActiveSessions/              # broadcast dir
    from-<orchestrator>.md
  toUser/                           # user-facing letters (glossaries, mid-task asks)
```

**Naming rules**

| Slot | Rule | Example |
|---|---|---|
| Directory | `to<Role>` PascalCase, role/function not session | `toOrchestrator`, `toIpcL2Author`, `toCliTeam` |
| Entry letter | `handoff.md` | the one document new session reads first |
| Outbound letter | `from-<sender>-<topic>.md` lowercase-kebab | `from-impl-a-blocker-spec-mismatch.md` |
| Bundle reply | `from-<sender>-<recipient>-<theme>.md` | `from-orche-impl-a-decisions.md` |

**Short role aliases** (e.g. `toBe`, `toFe`, `toOrche`) are acceptable in small projects, but stay consistent within a repo. Decisions/relays always use the `-decisions.md` suffix even for a single decision — it stays bundle-ready and recipients learn one shape.

## YAML frontmatter

**Single recipient** — frontmatter optional. Default lifecycle = burn when the recipient's lifecycle ends. A short `> from: / recipient: / purpose: / lifecycle:` quote block at the top is sufficient.

**Multi recipient** — frontmatter required:

```yaml
---
recipients:
  - role: <role>
    purpose: <why they read this>
    lifecycle: <termination condition, e.g. "until v0.1 ships" or "until superseded by <filename>">
on_lifecycle_end: burn | archive | persist
created: <YYYY-MM-DD>
created_in: <human-readable origin tag, e.g. "orche-2026-05-14" or "worktree:.worktrees/api-v1">
---
```

- `lifecycle` should be a concrete *condition* a future reader can evaluate without context (a date, a milestone name, a "superseded by" pointer). Avoid open-ended "until done".
- `created_in` is a human label, not a UUID — readable enough that an audit can trace which session produced the letter.
- For broadcasts, declare a supersession rule explicitly: either `until <event>` or `until superseded by <toAllActiveSessions/from-orche-...md>`.

- `burn` = `git rm` (default).
- `archive` = move to `docs/sendbox/archive/` for historical reference.
- `persist` = promote content into durable docs (architecture, memory, spec) via human-in-the-loop review, then burn the letter.

A letter with neither frontmatter nor a single clear recipient is **malformed**.

## Letter type catalog

Each row = a recurring pattern. Adopt these names so other agents recognize the shape on sight.

| Type | Trigger | Required content |
|---|---|---|
| `handoff.md` (two modes — see Handoff modes section) | New session spawned; needs entry context | Mode-dependent (child vs inheritance) — see dedicated section |
| `from-<x>-plan-ready.md` | Implementer finished planning; awaits greenlight | Step checklist, plan path, key decisions summary, list of items needing ack, risk signals |
| `from-<orche>-<x>-greenlight.md` | Orchestrator approves plan / unblocks | Acks per item, any binding constraints, go/no-go |
| `from-<x>-blocker-<topic>.md` | A-12 stop-and-ask: agent can't or shouldn't proceed unilaterally | TL;DR, mismatch/cause, 2-3 Options table (work / risk / time), implementer's tentative pick, snapshot of current state |
| `from-<orche>-<x>-decisions.md` | Orchestrator bundles multiple decisions to one recipient | Numbered decisions with rationale, action items per decision |
| `from-<x>-<milestone>-done.md` | Vertical slice or acceptance gate hit | Acceptance table with evidence, key technical findings, commit list, next-step request |
| `from-<x>-archived.md` / `closed.md` | Task complete, letter chain wrapping up | Final state, links to archive, what stays / what burns |
| `from-<x>-promote-to-durable.md` | Forced human review for promoting transient knowledge into a durable knowledge store | What is being promoted, why, diff summary, HITL gate |
| `toAllActiveSessions/from-<orche>.md` | Broadcast: new constraint, tool change, branch rename | Per-item heading, scope, action required, supersession rule |
| `toUser/<topic>.md` | User-facing glossary, mid-task ask, or persistent reference | Plain language, no internal slang, lifecycle stated |

## Communication patterns

### A-12 stop-and-ask (the most important pattern)

When you reach a boundary you can't cross unilaterally — scope mismatch with upstream, destructive change, ambiguity from upstream decision — STOP at the boundary. Do not merge / archive / push. Write `from-<you>-blocker-<topic>.md` with:

1. **TL;DR** in 2 sentences.
2. **Timeline** of what was delivered (commit hashes).
3. **Mismatch core** — quote the upstream directive verbatim, then state what was actually delivered.
4. **Options table** with 2-3 alternatives, each with work estimate + risk.
5. **Your tentative pick + why.** Don't be neutral if you have a view; upstream needs your judgment.
6. **Current snapshot** — worktree HEAD, test status, what is committed vs uncommitted.

Stop. Wait for ack. Do not silently proceed with your preferred option.

### Multi-decision ack bundle

When an orchestrator owes responses to N items at once, write ONE bundled letter with numbered decisions, not N letters. Each decision: `### Dn — <topic>` → answer → action item. Easier to read; easier to burn.

### Escalation with options

When presenting options upstream, structure as: `(a) preferred-by-you / (b) safer-fallback / (c) defer-to-user`. Always include time estimates and risk class. Avoid "what do you want me to do?" with no proposed answers — that's an information-free letter.

### Cross-cwd write (subagent whose working directory ≠ main agent's)

By Core Principle 0, the main agent's `docs/sendbox/` is canonical. Any subagent whose cwd is *not* the project root must reach that path explicitly. Three common cases:

| Subagent cwd | Where to write the letter |
|---|---|
| `<project>/.worktrees/<task>/` (same repo, different worktree) | Either commit on the worktree branch (the letter rides with the merge), OR write to `<project>/docs/sendbox/to<Role>/...` via relative `../../` traversal (the letter is visible to main *before* the merge) |
| Completely different repo or `/tmp/...` (different cwd, no shared git tree) | Write to the main agent's sendbox by **absolute path** (`/home/.../<main-project>/docs/sendbox/to<Role>/...`). Do NOT create `docs/sendbox/` under your own cwd |
| Same project, same cwd (just a peer session) | Write to `docs/sendbox/to<Role>/...` directly — trivial case |

**Default channel for case 1** (same-repo, different worktree): write directly to main when the letter must be visible *before* the worktree merges (blocker, plan-ready awaiting greenlight). Commit on worktree when the letter rides along with the change (milestone-done bundled with the merge).

**Hard rules across all cases:**
- Never create a parallel `docs/sendbox/` under a side cwd. Sendboxes do not fan out — there is exactly one per project, held by the main agent.
- Never dual-write to both your local path and the main path "for symmetry". You will forget to push one side; pick one channel per letter.
- If the subagent cannot reach the main agent's path (sandbox / permission boundary), it MUST surface that to the user rather than write a shadow sendbox.

### Broadcast

For information all active sessions must absorb (tool installed, branch renamed, gate added), use `toAllActiveSessions/from-<orche>.md` with multi-recipient frontmatter and a clear supersession rule.

## Handoff modes — child vs inheritance

The `handoff.md` letter (entry letter for a newly-spawned session) has two semantic modes, dictated by the **lifecycle relationship** between the spawning session (parent) and the spawned session. The mode determines what the letter should contain.

Declare the mode in the letter's body — the top-line quote block (`> mode: child` or `> mode: inheritance`) is sufficient. The filename stays `handoff.md` either way.

### Mode A — child-handoff (parent persists; child converges back)

The spawning session continues to live; the new session is a bounded subordinate handling one subtask, then dying. Convergence is part of the contract.

**Required content:**

| Slot | Content |
|---|---|
| Subtask scope | 1-2 paragraphs: what specifically, success criteria |
| Inputs | Files / paths / references the child needs — the *minimum* set |
| Convergence path | Where to write back when done (`from-<child>-<milestone>-done.md` to parent's `to<ParentRole>/`); where to write if stuck (`from-<child>-blocker-<topic>.md`) |
| Parent's cwd (absolute path) | Required if the child is in a different cwd / worktree / repo — child writes back by absolute path per Cross-cwd write rules |
| Out-of-scope explicit statement | What NOT to do (prevents scope creep) |

**Forbidden content:**

- Full project context dump (version-plan, full architecture, broad history)
- Reading lists beyond what the subtask requires
- Pending unrelated decisions / open questions

A child should re-fetch context as needed, not receive a context dump.

**Lifecycle:** burn after the child converges (parent reads done letter, both letters archive or burn).

### Mode B — inheritance-handoff (parent ends; inheritor takes ownership)

The spawning session is about to terminate (session compaction, orchestrator handover, role transfer). The new session inherits the parent's full active responsibility. There is no convergence back — the parent will be gone.

**Required content:**

| Slot | Content |
|---|---|
| Identity statement | "You are <role>, you inherit <parent>'s responsibility for <scope>" |
| Status snapshot | Table: done / in-flight / pending — concrete commits / letters / blockers |
| Must-read list | version-plan, recent letters, active blockers, architecture docs — the full reading set |
| Pending decisions / open questions | Anything the parent didn't finalize and the inheritor must |
| Active relationships | Other sessions / repos this responsibility coordinates with |
| Env / tool state | Uncommitted changes, worktree state, branch HEAD |
| Lifecycle disposition | When this letter itself burns (typically after inheritor logs first milestone) |

**Forbidden content:**

- A convergence path back to parent — parent is gone; there is no parent
- Subtask scope narrower than the full inherited responsibility (this is not a subtask; it is a role transfer)

**Lifecycle:** `persist` for audit until inheritor's first milestone-done letter, then burn (the inheritor's first delivered milestone implicitly ratifies the handoff).

### Mode C — peer dialogue (not a handoff)

If two sessions already exist and need to coordinate (neither inherits or owns the other), this is **not** a handoff pattern. Peer relationships are typically orchestrator-mediated: an orchestrator spawns A and B each via Mode-A child-handoffs, and A and B coordinate peer-to-peer using normal letter types (blocker, decisions, milestone-done). No peer-handoff letter type exists; do not invent one.

If A genuinely needs to delegate a subtask to B mid-flight, A becomes B's parent for that subtask — file a Mode-A child-handoff from A to B.

### Mode detection rule

When writing a `handoff.md`, ask:
- "Does the spawning session continue to live after this handoff?"
  - **Yes** → Mode A (child)
  - **No** → Mode B (inheritance)
- "Does the new session need to report back?"
  - **Yes** → Mode A (child)
  - **No** → Mode B (inheritance)

Both questions should give the same answer for a well-formed handoff. If they disagree (parent stays alive but new session does not report back), the relationship is ambiguous — clarify before writing the letter.

## Anti-patterns

**1. Writing letters for chat-synchronous decisions.** If the user answered in chat AND the decision has no upstream follow-up AND no other session is affected → just commit (`per chat decision YYYY-MM-DD: <one-line summary>`). Writing a letter to mirror a chat decision is the #1 noise source. **Scope**: this rule targets the *worktree session replying upstream after the user already answered in its own chat*. It does NOT exempt an orchestrator relaying a substantive directive to a *detached* implementer — that implementer never saw the chat, so the letter is delivery, not duplication. Triviality bar: skip the letter only when the decision will be picked up naturally by a commit the recipient is about to rebase on.

**2. Dual-syncing worktree → main.** Writing the same letter into both `.worktrees/<task>/docs/sendbox/...` and `docs/sendbox/...` *will* cause one side to be forgotten. Pick the single canonical path.

**3. Localized or special-char filenames.** Letters cross worktrees, branches, and shells. Stick to `[a-z0-9-]+\.md`.

**4. Using sendbox as state tracking.** If your letter says "status: in_progress", you have a dashboard problem, not a sendbox problem. State lives in a dashboard or spec; letters *trigger* state changes.

**5. Rotting unburned letters.** Every letter has a lifecycle. When the lifecycle ends, `git rm` is mandatory. A `docs/sendbox/` directory that only grows is a broken sendbox. Run periodic audits.

**6. Letters as narrative.** Letters are operational. No "in session X we found that…" — write the rule, the option, or the request. History belongs in commit messages.

**7. One letter per micro-decision.** Bundle when you can. Five small letters to the same recipient = one bundled letter.

## Cleanup checkpoints

Lifecycle dispositions don't execute themselves. The letter's author and recipient share responsibility for triggering cleanup at two specific moments. Make these checkpoints explicit in agent discipline; do NOT defer "until later" — the next session may be a different role and miss the cue.

### Checkpoint 1 — at session end

Before terminating, scan `docs/sendbox/` for (a) letters you authored and (b) letters addressed to your role. For each letter, evaluate its lifecycle condition. If the condition has triggered, execute the disposition immediately:

| Disposition | Action |
|---|---|
| `burn` | `git rm <letter>` and commit (`sendbox: burn <letter> (lifecycle ended)`) |
| `archive` | move to `docs/sendbox/archive/<letter>` |
| `persist` | confirm the content has been promoted to durable docs, then burn the letter (persist means *promoted-then-burned*, not *kept forever*) |

### Checkpoint 2 — at task convergence

When a task chain completes, sweep the full letter chain associated with that task. Typical pairs to burn together:

| Trigger event | Burn pair |
|---|---|
| Child reports `milestone-done` to parent | `handoff.md` (parent→child) + `from-<child>-milestone-done.md` |
| Inheritance handoff: inheritor logs first milestone-done | `handoff.md` (inheritance) — the milestone implicitly ratifies the transfer |
| A-12 blocker resolved | `from-<x>-blocker-<topic>.md` + `from-<orche>-<x>-decisions.md` (the answer) |
| Plan greenlit | `from-<x>-plan-ready.md` + `from-<orche>-<x>-greenlight.md` |
| Broadcast superseded | the prior broadcast letter (supersession event fires) |

Burning matched pairs together prevents "half-completed conversations" cluttering the sendbox dir.

### Why active sweeping (vs reactive audit)

Anti-pattern #5 (rotting unburned letters) describes the *symptom*. These checkpoints are the *prevention*. An adopter who runs cleanup proactively at the two checkpoints above never accumulates rot; reactive audit becomes a backstop, not a routine.

### What stays (persist disposition exemption)

Letters whose lifecycle is explicitly `persist` (e.g. `from-sendbox-self-desc.md`, `toUser/glossary.md`, embedding playbooks) stay across session ends and task convergences. They are reference docs that happen to live in sendbox dir — the checkpoint sweep skips them. Re-evaluate `persist` letters only on substantive content change.

## Interop with other protocols

Sendbox is framework-agnostic. It coexists with whatever workflow / spec / memory / dashboard system the host project already uses. The general principles below apply regardless of the specific framework:

- **Pipeline-style workflows with verification gates / HITL approvals**: ask-first and HITL steps are natural sendbox triggers. Plan-ready, blocker, and merge-pre-archive checkpoints typically produce a sendbox letter.
- **Spec systems** (any contract-style spec layer): spec contracts are durable; sendbox is transient. A `blocker` letter may *cite* a spec but never *replace* one.
- **Long-term knowledge stores** (any persistent memory / architecture-doc layer): if a sendbox letter contains a lesson worth keeping, promote it to the knowledge store and burn the letter. Letters are not long-term memory.
- **Dashboards** (orchestrator state boards): dashboards hold *current* state; sendbox holds *requests for change*. An orchestrator reading a blocker letter updates the dashboard, then burns or archives the letter.

Framework-specific embedding mechanics (how to actually slot sendbox into a given workflow stack) belong outside this skill, in framework-targeted playbook letters under `docs/sendbox/to<Framework>Factory/`. The skill stays framework-agnostic; each adopting framework writes its own playbook.

## Quick start (drop into a fresh repo)

1. Pick the **main agent's project root** — the one repository whose `docs/sendbox/` will be canonical. Run `mkdir -p docs/sendbox/toOrchestrator docs/sendbox/toUser docs/sendbox/toAllActiveSessions` there, and only there. Subagents in side cwds will reach this path by relative traversal or absolute path; they do NOT create their own sendbox.
2. When spawning a session, write `docs/sendbox/to<Role>/handoff.md` with status snapshot + must-reads + day-1 actions. No frontmatter if single recipient.
3. When the session needs to reply: `docs/sendbox/to<UpstreamRole>/from-<yourname>-<topic>.md`. Use the type catalog to pick a name.
4. When you can't proceed: write a `from-<you>-blocker-<topic>.md` with 2-3 options, stop, wait.
5. When lifecycle ends: `git rm` the letter. Commit message: `sendbox: burn <letter> (lifecycle ended)`.
6. Audit weekly: `ls docs/sendbox/**/*.md` — any letter older than its stated lifecycle should already be gone.

## Red flags — you are probably misusing sendbox

- Writing a letter to mirror a chat decision the orchestrator made 5 minutes ago.
- Letter directory grows monotonically with no `git rm` commits.
- Letters titled `status-update-N.md` — that's a dashboard.
- Filename contains non-ASCII characters or spaces.
- Letter has no recipient role and no frontmatter — malformed.
- Same letter copied into both worktree and main paths.
- A `docs/sendbox/` directory exists under a subagent's cwd (a `.worktrees/...` path, a `/tmp` dir, or a sister repo) — sendboxes do not fan out; there is one per project held by the main agent.
- "Quick clarification" letters that don't ask, decide, or escalate — just narrate.

All of these mean: stop, delete the letter, use the right channel.

## See also

- [`cc-dashboard`](https://github.com/JasonJarvan/cc-dashboard) — projection skill that maintains a single source of truth (`docs/Dashboard/index.md`) for pending user actions distilled from sendbox letters, handoff briefs, and external trackers. Recommended companion when multi-implementer orchestration accumulates user-blocking asks across many `toUser/` letters; turns "user has to read 7 letters to find pending sub-asks" into "user opens one markdown table".
