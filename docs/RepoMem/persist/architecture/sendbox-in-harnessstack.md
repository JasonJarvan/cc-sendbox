---
domain: sendbox-protocol
last_reviewed_at: 2026-05-16
---

# sendbox 在 HarnessStack 体系中的定位

Canonical for "where sendbox sits structurally within HarnessStack". Cited from `docs/sendbox/toAllAgents/from-sendbox-self-desc.md` §5; embedding mechanics live separately in `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md`. Promoted from `docs/RepoMem/temp/sendbox-in-harnessstack/design.md` §A + §B on 2026-05-16 via HITL merge.

## 1. Positioning — cross-method invariant

`sendbox-protocol` is a **cross-method invariant** in HarnessStack — not a pipeline step, not a layer. It does not insert any new step into the 11-step pipeline, does not modify step ordering, and does not require per-task opt-in.

Trigger condition: a task crosses session boundaries (subagent dispatched / multi-worktree work / cross-cwd handoff). When the trigger fires, the canonical channel for asynchronous coordination is sendbox; when it does not, sendbox is silent.

### Rationale for rejecting the alternatives

| Alternative | Rejected because |
|---|---|
| Pipeline step (add a new step to the 11-step pipeline) | Forces every task through sendbox even when work is solo single-session and sendbox has zero value. Pure overhead. |
| Layer (analogue to OpenSpec / ECC, declared in long-term contract, opted in per-task via `temporary-<task>.md`) | Too heavy for what is essentially a communication discipline. Layers imply spec contracts, verification gates, archive cycles — sendbox is none of these. |
| Suitability Envelope extension condition (only fits when multi-session) | Would force the contract to flip whenever work mode shifts, churning `longterm.md` for no structural reason. |
| Cross-method invariant ✅ | Right granularity: declared once at long-term contract level; applied conditionally on trigger; no per-task ceremony. |

### How it overlays the pipeline

sendbox triggers map onto existing pipeline steps without inserting new ones — see `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md` §3.3 for the trigger reference table (brainstorming / executing-plans / verification / requesting-code-review / finishing-a-development-branch / RepoMem.merge / any-time-broadcast).

## 2. Documentation layering — customer-routing principle

cc-sendbox documents are routed by **who reads them**, not by topic. This is a durable principle that survives skill evolution.

| Customer | Path | Role |
|---|---|---|
| User (human end-user) | `README.md` | install + usage entry |
| AllAgents (any agent reader, framework-agnostic) | `docs/sendbox/toAllAgents/from-sendbox-self-desc.md` | what it is, when to use, hard rules, pointers |
| HarnessFactory (HS factory agent embedding sendbox into a new HS repo) | `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md` | decision tree + embed checklist + invariant template |
| Skill caller (any framework, may not be HS) | `skills/sendbox-protocol/SKILL.md` | protocol body — **must stay framework-agnostic** |
| Own HS contract (cc-sendbox self) | `docs/HarnessStack/longterm.md` § Cross-Method Invariants | one-line pointer to skill + playbook, never inline skill body |
| Durable architecture (this file) | `docs/RepoMem/persist/architecture/sendbox-in-harnessstack.md` | cross-task structural decisions |

### Why customer-routing

1. **Noise reduction** — each reader sees only what concerns them; the HS-specific decision tree is noise for a generic agent, and a self-desc is noise for a factory operator who already knows what sendbox is.
2. **Skill portability** — keeping framework-specific concerns out of SKILL.md preserves the skill's value as a portable building block across frameworks (BMAD / gstack / future HS variants).
3. **Extensibility** — additional frameworks adopting sendbox land at parallel paths (`toBmadFactory/`, `toGstackFactory/`); no `Interop with X` section of SKILL.md ever grows.
4. **Single-source-of-truth** — each fact lives in exactly one document. Cross-references are pointers, not duplications. `longterm.md` cites the skill and playbook; never copies them.

### Hard rule for skill maintainers

When considering an edit to `skills/sendbox-protocol/SKILL.md`, run `grep -E 'HarnessStack|OpenSpec|ECC|RepoMem|repomem|17-step|auto-memory'` on the result. Any match indicates framework leakage and must be either (a) removed, (b) generalized to framework-agnostic vocabulary, or (c) moved to the appropriate customer-targeted letter or doc.

## 3. Application scope

Applies to all HS-managed repositories that adopt sendbox via the embedding playbook. The playbook's decision tree (recipe × scale × work-mode in §2 of the playbook) gates whether a target repo qualifies for embedding. This architecture doc tells embedded targets *what shape* sendbox takes structurally inside HS; the playbook tells the factory *how to install and wire it*.

## 4. Sister knowledge

- [[minimal-sendbox-scope]] — reachability rule for chat decisions vs sendbox letters (when not to write a letter)
- [[canonical-sendbox-and-main-agent]] — main-agent definition and fan-out prevention (the implementation-level invariant)

This architecture entry sits structurally above both: it answers "where does sendbox slot into HS"; the two memory entries answer "when do I write a letter" and "where does the letter go".

## 5. Re-evaluation triggers

Revisit this architecture if any of:

- sendbox gets embedded into a non-HS framework (BMAD / gstack / GSD) — may surface a more general invariant pattern that needs lifting out of this HS-specific file
- A real HS repo case shows sendbox-as-pipeline-step actually fits — would refute the cross-method-invariant choice and force a re-positioning
- Customer-routing principle expands beyond sendbox docs (e.g. RepoMem-related docs start routing the same way) — may promote a more general memory entry on documentation organization
- A breaking change to SKILL.md skill body affects the playbook's invariant template — sync this file's §1 trigger condition language to match

## 6. Promotion provenance

- Source: `docs/RepoMem/temp/sendbox-in-harnessstack/design.md` §A + §B
- Brainstorm: 2026-05-15 → 2026-05-16 (one session)
- Pre-promotion artifacts already live (not promoted, they are production docs not knowledge):
  - `skills/sendbox-protocol/SKILL.md` §Interop framework-agnostic (commit cc22e11)
  - `docs/HarnessStack/longterm.md` § Cross-Method Invariants invariant added (commit cc22e11)
  - `docs/sendbox/toAllAgents/from-sendbox-self-desc.md` (commit 34e8dc9)
  - `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md` (commit 34e8dc9)
