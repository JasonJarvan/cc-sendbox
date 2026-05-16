---
slug: sendbox-in-harnessstack
status: merged
domains:
  - sendbox-protocol
updated_at: 2026-05-16
task_type: feature
merge_target: docs/RepoMem/persist/architecture/sendbox-in-harnessstack.md
merge_date: 2026-05-16
merge_sections: §A + §B
---

# Design: sendbox 在 HarnessStack 体系中的嵌入

**Status**: brainstorming approved 2026-05-15. Implementation complete + HITL merge done 2026-05-16. §A + §B promoted to `docs/RepoMem/persist/architecture/sendbox-in-harnessstack.md`. §C–§H stay here as task-scoped residue (do not merge per merge rules).

**Single Task Identifier**: `sendbox-in-harnessstack`
- HarnessStack `<task>`: `sendbox-in-harnessstack`
- RepoMem `<slug>`: `sendbox-in-harnessstack`
- OpenSpec: not opted in (cc-sendbox recipe `superpowers-repomem` has OpenSpec inactive)

**Recipe Invariant Exception**: skip `temporary-<task>.md`. **Reason**: task is a brainstorming follow-up with ~6 mechanical implementation sub-steps (rename / split / new letter / 2 in-file edits / verify) and <3h estimated effort; the HarnessStack pipeline `auto-judge` skip criteria are met. **Compensating action**: this design doc carries the task contract role; commit messages cite the chat decision; new auto-memory will be promoted via the normal `RepoMem.merge` HITL.

## A. Positioning

`sendbox-protocol` is a **cross-method invariant** in HarnessStack — not a pipeline step, not a layer. When a repo's work crosses session boundaries (multiple worktrees, dispatched subagents, multi-cwd execution), sendbox is the canonical asynchronous channel. The HarnessFactory agent decides *whether* a new HS repo needs sendbox; cc-sendbox is the embedded artifact, not the embedder.

This decision was made because:
1. Adding a pipeline step would force every task through sendbox even for solo single-session work where it has zero value.
2. Adding a layer (like OpenSpec / ECC) implies per-task opt-in via temporary contract — too heavy for what is essentially a communication discipline.
3. Cross-method invariant is the right granularity: declared once in `longterm.md`, applied whenever its trigger condition (multi-session work) is true, no per-task ceremony.

## B. Document ownership by customer

User-stated principle: route documentation by *who reads it*, not by topic.

| Customer | Path | Role |
|---|---|---|
| **User** (human end-user) | `README.md` | install + usage entry |
| **AllAgents** (any agent reader, framework-agnostic) | `docs/sendbox/toAllAgents/from-sendbox-self-desc.md` | what it is, when to use, hard rules, pointers |
| **HarnessFactory** (HS factory agent embedding sendbox into a new HS repo) | `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md` | decision tree + embed checklist + invariant template |
| **Skill caller** (any framework) | `skills/sendbox-protocol/SKILL.md` | protocol body, **framework-agnostic** — no HS-specific terms |
| **Own HS contract** (cc-sendbox self) | `docs/HarnessStack/longterm.md` § Cross-Method Invariants | one-line pointer to skill + playbook, no inlined body |
| **Durable architecture** (cross-task, promoted) | `docs/RepoMem/persist/architecture/sendbox-in-harnessstack.md` | after HITL merge from this temp doc |

Why split AllAgents and HarnessFactory rather than fold both into one:
- Overlap ~70% (definition / hard rules / pointers)
- HarnessFactory has 3 framework-specific blocks (decision tree / pipeline-trigger map / longterm.md diff template) that are noise for a generic agent reader
- Future: if sendbox is embedded into other frameworks (BMAD, gstack, etc.), parallel structure `toBmadFactory/` extends cleanly without polluting AllAgents self-desc

## C. Outline per document (delta from current state)

### C.1 `toAllAgents/from-sendbox-self-desc.md`

**Action**: rewrite current `toAll/from-sendbox-self-desc.md` removing HS-specific sections.

Keep: §1 What this repo is · §2 What the skill is for (triggers + skip rules) · §3 Boundaries · §4 Hard rules (main agent / canonical / no-fan-out / lifecycle) · §5 Pointers (SKILL.md, GitHub, RepoMem) · §6 This letter's own lifecycle.

Remove: §5 HS pipeline ↔ trigger map (moves to playbook) · §6 Factory checklist (moves to playbook).

Identity in frontmatter: `recipients` reduces to one role (AllAgents), but per skill multi-recipient rule still keep YAML frontmatter for self-desc lifecycle clarity.

### C.2 `toHarnessFactory/from-sendbox-embedding-playbook.md` (new)

5 sections:

1. **Audience + prerequisite**: you are a HarnessFactory agent producing or upgrading an HS-managed repo and deciding whether sendbox belongs. Pre-req read: `../toAllAgents/from-sendbox-self-desc.md`.
2. **Decision tree** — embed sendbox when ALL of: (recipe ≥ `superpowers-repomem` OR the target plans multi-agent orchestration explicitly) AND (work mode involves dispatched subagents OR multi-worktree). Three-dimensional: recipe × scale × work-mode.
3. **Embedding checklist** — (3.1) install skill (symlink for solo; plugin manifest once v0.2 ships) · (3.2) one-line invariant added to target `longterm.md` § Cross-Method Invariants · (3.3) pipeline ↔ sendbox trigger reference map (which HS step typically produces which letter type) · (3.4) verification: target CLAUDE.md does not override skill's hard rules.
4. **Anti-patterns** — what NOT to do when embedding: do not inline skill body; do not scaffold empty `docs/sendbox/`; do not embed sendbox into a `superpowers` (no-repomem) recipe unless explicitly multi-session.
5. **Copy-paste invariant template** — exact text the factory pastes into the target `longterm.md` § Cross-Method Invariants.

### C.3 `skills/sendbox-protocol/SKILL.md` §Interop edit

Replace HS-specific terms with framework-agnostic equivalents:
- "17-step pipeline / HITL gates" → "pipeline-style workflows with verification gates / HITL approvals"
- "OpenSpec / spec docs" → "spec systems"
- "Memory / auto-memory" → "long-term knowledge stores"
- "Dashboards" stays generic

Goal: SKILL.md is portable across BMAD / gstack / HS / etc. No framework name leaks.

### C.4 `docs/HarnessStack/longterm.md` § Cross-Method Invariants

Append exactly one bullet:

> **Multi-session work uses sendbox-protocol.** When a task involves multiple sessions, dispatched subagents, or cross-worktree coordination, the `sendbox-protocol` skill (see `skills/sendbox-protocol/SKILL.md`) is the canonical asynchronous channel; the main agent's `docs/sendbox/` is the single source of truth and subagents in side cwds write to it by relative or absolute path. See `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md` for embedding mechanics in derived HS repos.

## D. Migration of current `toAll/from-sendbox-self-desc.md`

| Original section | Goes to |
|---|---|
| §1 What this repo is | self-desc §1 (keep) |
| §2 What the skill is for | self-desc §2 (keep) |
| §3 Boundaries | self-desc §3 (keep) |
| §4 Hard rules | self-desc §4 (keep) |
| §5 HS pipeline ↔ trigger map | playbook §3.3 |
| §6 Factory checklist | playbook §3 (expanded with decision tree + template) |
| §7 Pointers | self-desc §5 (keep) |
| §8 Lifecycle | self-desc §6 (keep) |

Operation: `git mv docs/sendbox/toAll docs/sendbox/toAllAgents`, then rewrite `from-sendbox-self-desc.md` minus the two transplanted sections.

## E. Implementation sequence and commit boundaries

Sub-tasks (mirrored in TaskCreate list 6-11):
1. Write this design doc (current step)
2. Rename `toAll` → `toAllAgents` + rewrite self-desc — one commit: `sendbox: split self-desc letter, framework-agnostic`
3. Write embedding playbook — one commit: `sendbox: add HarnessFactory embedding playbook`
4. SKILL.md Interop framework-agnostic edit — one commit: `skill(sendbox-protocol): de-frameworkize Interop section`
5. longterm.md cross-method invariant — one commit: `harness: codify sendbox cross-method invariant`
6. Verification + push

Commits stay logical-unit-sized for readable history.

## F. Verification

Lightweight outsider check on the embedding playbook: fresh subagent reads only the playbook (plus pre-req self-desc), receives a hypothetical HS repo scenario (recipe + scale + work-mode), and must decide correctly whether to embed sendbox + cite playbook rules. Pass criterion: correct decision + cited rule for each scenario.

Final pass before commits: confirm SKILL.md word count stays reasonable (target ≤ 2400 after Interop edit, currently 2271) and no HS-specific term leaks remain via grep.

## G. Post-implementation: RepoMem.merge candidate

This `temp/sendbox-in-harnessstack/design.md` is a promotion candidate. After verification + commits land, surface a `from-implementer-repomem-merge-promotes.md` letter (per skill catalog) summarizing the durable architectural insight worth promoting to `docs/RepoMem/persist/architecture/sendbox-in-harnessstack.md`. User does HITL merge. On accept, this temp file is deleted; on reject, it stays in temp.

## H. Spec self-review (per brainstorming step 7)

- Placeholder scan: no TBD / TODO; one explicit Recipe Invariant Exception declared for skipping `temporary-<task>.md`
- Internal consistency: B/C/D/E mutually consistent — sections always reference the same `toAllAgents` / `toHarnessFactory` paths
- Scope check: focused on one task (embedding); no scope creep into "redesign all of HarnessStack"
- Ambiguity check: §C.3 SKILL.md Interop terms are listed exhaustively; §C.4 invariant text is final wording, not paraphrase
