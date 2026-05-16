---
recipients:
  - role: HarnessStack factory agent producing or upgrading an HS-managed repository, deciding whether and how to embed sendbox-protocol
    purpose: drive the embed/skip decision and execute the embed correctly when needed
    lifecycle: persist (canonical embedding playbook; updates supersede in place)
on_lifecycle_end: persist
created: 2026-05-16
created_in: main agent in cc-sendbox repo, drafted during sendbox-in-harnessstack brainstorm
---

# Embedding playbook — `sendbox-protocol` into a HarnessStack repo

This letter tells a HarnessFactory agent (1) whether the target repo needs sendbox, and (2) exactly what to do to embed it. It is HS-specific. Generic readers should consult the [`../toAllAgents/from-sendbox-self-desc.md`](../toAllAgents/from-sendbox-self-desc.md) self-description instead.

## 1. Audience + prerequisite

You are a HarnessStack factory agent. You have just produced or are about to upgrade a target HS repo and must decide whether `sendbox-protocol` belongs in it. **Required pre-read** (≤3 min): [`../toAllAgents/from-sendbox-self-desc.md`](../toAllAgents/from-sendbox-self-desc.md). Everything below assumes you have read it and know what sendbox is.

Authoritative skill source: [`/home/zhaosheng/Codes/cc-sendbox/skills/sendbox-protocol/SKILL.md`](../../../skills/sendbox-protocol/SKILL.md) or upstream `~/.claude/skills/sendbox-protocol/`.

## 2. Decision tree — embed or skip?

Embed sendbox into the target HS repo when **both** dimensions below hold:

### 2.1 Recipe / scale dimension

| Target recipe | Embed sendbox? |
|---|---|
| `superpowers` (no RepoMem) | Skip by default — single-session execution discipline; sendbox adds ceremony |
| `superpowers-repomem` (solo, platform/library) | Embed iff §2.2 also says yes (most solo platform repos do NOT need it) |
| `openspec-superpowers-repomem` (small-team product) | Embed by default — multi-task work commonly spawns subagents |
| `openspec-superpowers-repomem-ecc` (small-team product, hardened) | Embed by default |
| Any recipe extending a primary-workflow layer (BMAD / gstack / GSD) | Embed by default |

### 2.2 Work-mode dimension

Embed if **any** of these is plausible in the target repo's near-term work:

- A task may be dispatched to a **subagent in a different cwd / worktree / repo**
- The repo will run **multi-worktree parallel work** (orchestrator + ≥1 implementer)
- The repo will produce **A-12 stop-and-ask boundaries** that need durable record (e.g. blocker letters surviving session reset)
- The repo communicates with another HS repo via a **cross-repo handoff** mechanism

If none of the above is plausible (truly solo single-session work), skip the embed even if §2.1 says embed-by-default. Re-evaluate when work mode changes.

### 2.3 Skip outcome

If skipping: do NOT create `docs/sendbox/`, do NOT touch `longterm.md`. Record the decision in the target repo's `version-plan[.{lang}].md` "future considerations" / "未来考虑" or equivalent so a future factory pass can reconsider.

## 3. Embedding checklist (when §2 says embed)

### 3.1 Install the skill

Pick the cheapest mechanism the target user already has:

- **Symlink** (lowest ceremony):
  ```
  ln -s /home/<user>/Codes/cc-sendbox/skills/sendbox-protocol \
        ~/.claude/skills/sendbox-protocol
  ```
- **Plugin manifest** (when cc-sendbox v0.2 ships one — pending; see cc-sendbox `version-plan[.{lang}].md` v0.2 candidate #1)

Verify: Claude Code recognises `sendbox-protocol` in the skill list at next session start.

### 3.2 Add one cross-method invariant to target `longterm.md`

Paste the §5 template verbatim into the target's `docs/HarnessStack/longterm.md` § Cross-Method Invariants. Do NOT inline the skill body. Do NOT paraphrase the wording — the wording is part of the contract.

### 3.3 Pipeline ↔ sendbox trigger reference

Do not insert new pipeline steps. Sendbox is triggered by *conditions* that overlay existing steps:

| HS pipeline step | Condition that triggers a letter | Canonical letter type |
|---|---|---|
| Step 2 brainstorming (multi-session work) | Spawning a fresh recipient session | `handoff.md` to `to<Role>/` |
| Step 5 worktree + executing-plans | Implementer awaits orchestrator greenlight | `from-<sender>-plan-ready.md` |
| Step 5 (during implementation) | A-12 stop-and-ask hit (boundary cannot be crossed unilaterally) | `from-<sender>-blocker-<topic>.md` |
| Step 7 verification | Vertical-slice acceptance gate hit | `from-<sender>-<milestone>-done.md` |
| Step 8 requesting-code-review | Orchestrator owes N decisions to one recipient | `from-<orche>-<recipient>-decisions.md` (bundled) |
| Step 9 finishing-a-development-branch | Branch close-out summary | `from-<sender>-archived.md` |
| Step 10 RepoMem.merge HITL | Promotion candidates from temp → persist | `from-<sender>-repomem-merge-promotes.md` |
| Any time | New constraint applies to all active sessions | `toAllActiveSessions/from-<orche>.md` (broadcast) |

This map is *reference*, not enforcement. Only write a letter when the [Minimal Sendbox rule](../../../skills/sendbox-protocol/SKILL.md) says you should.

### 3.4 Verification (factory side, before declaring embed done)

- Target's `CLAUDE.md` (if it has one) does NOT override or weaken any of the four hard rules listed in `toAllAgents/from-sendbox-self-desc.md` §4.
- Target's `docs/sendbox/` is NOT scaffolded yet — it appears the first time a letter is actually written.
- `~/.claude/skills/sendbox-protocol` is reachable from the target user's Claude Code env.
- The invariant text in §3.2 is verbatim, not paraphrased.

## 4. Anti-patterns (what NOT to do when embedding)

1. **Do not inline the skill body** into the target's `longterm.md`. Reference; do not duplicate. The skill is the single source of truth — duplicated copies drift.
2. **Do not scaffold `docs/sendbox/`** with empty subdirectories. Let it grow as letters are written.
3. **Do not embed into a `superpowers`-only recipe** (no RepoMem, no multi-method coordination) unless §2.2 explicitly says so. Sendbox without surrounding methods is empty ceremony.
4. **Do not modify the skill** in the target repo. If the target has a legitimate gap, file an issue against `cc-sendbox` upstream; do not local-fork.
5. **Do not write the invariant in your own words.** The §5 template is contract wording.

## 5. Copy-paste invariant template

Paste this **verbatim** into the target's `docs/HarnessStack/longterm.md` under § Cross-Method Invariants (add it as the last bullet):

```markdown
- **Multi-session work uses sendbox-protocol.** When a task involves multiple
  sessions, dispatched subagents, or cross-worktree coordination, the
  `sendbox-protocol` skill (see `~/.claude/skills/sendbox-protocol/SKILL.md`)
  is the canonical asynchronous channel. The main agent's `docs/sendbox/` is
  the single source of truth, and subagents in side cwds write to it by
  relative or absolute path — never fan out under their own cwd. See
  `cc-sendbox/docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md`
  for the embedding playbook.
```

If the target repo bundles a vendored copy of cc-sendbox (rather than referencing via `~/.claude/skills/`), adjust only the path in the parenthetical reference; leave the rest unchanged.

## 6. This letter's own lifecycle

`persist` — this playbook is the canonical embedding contract for HS factory agents. Updates: in place when sendbox evolves or new recipes appear. Replaced wholesale only if `sendbox-protocol` itself is renamed or superseded.

## 7. Pointers

- Generic self-desc: [`../toAllAgents/from-sendbox-self-desc.md`](../toAllAgents/from-sendbox-self-desc.md)
- Skill body: [`../../../skills/sendbox-protocol/SKILL.md`](../../../skills/sendbox-protocol/SKILL.md)
- Brainstorm design that produced this letter: [`../../RepoMem/temp/sendbox-in-harnessstack/design.md`](../../RepoMem/temp/sendbox-in-harnessstack/design.md)
- GitHub: https://github.com/JasonJarvan/cc-sendbox
