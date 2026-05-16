# longterm.md

## Document Meta

- Repository: `cc-sendbox`
- Effective From: 2026-05-15
- Source Template: `harness-factory/assets/templates/longterm/solo.md`
- Recipe Reference: `harness-factory/assets/templates/recipes/superpowers-repomem.md`
- OpenSpec Root: (blank — Spec Layer is not OpenSpec in this recipe)
- Last Updated Because: initial long-term contract; solo + long-lived platform repository needs durable cross-task memory but does not yet need a spec layer.

## Current Long-Term Assessment

- Collaboration Scale: `solo`
- Repository Type: `platform`
- Project Horizon: `long-lived`
- Long-Term Knowledge Governance: `enabled`

## Current Active Long-Term Baseline

### 1. Primary Workflow Layer

- Default enabled: none
- Emergent Ownership: Superpowers brainstorming and writing-plans carry primary workflow responsibility. RepoMem provides cross-task persistence but does not own workflow.
- Default inactive: `BMAD`, `gstack`, `GSD`
- Upgrade trigger:
  - repository enters roadmap-heavy delivery mode
  - downstream consumer count grows past what execution-only discipline can carry
  - cross-version compatibility planning becomes a recurring concern

### 2. Spec Layer

- Default enabled: none
- Default inactive: `OpenSpec`
- Upgrade trigger:
  - a task requires stable change boundaries for downstream consumers
  - delivery target reaches `MVP` or above
  - public API or contract surface needs an explicit changelog

### 3. Execution Discipline Layer

- Default enabled: `Superpowers`
- Default inactive: none
- Upgrade trigger:
  - raise verification and review intensity when risk rises (e.g., breaking API changes)

### 4. Repository Memory Layer

- Default enabled: `RepoMem`
- Default inactive: none
- Upgrade trigger:
  - long-term memory pressure requires `RepoMem.prune / split`
  - memory governance needs to harden into `ECC` hooks

### 5. Harness Enhancement Layer

- Default enabled: none
- Default inactive: `ECC`, `ECC(light)`
- Upgrade trigger:
  - agent context quality, safety, or repeatability becomes a recurring problem
  - memory or verification loops would benefit from harness-level hooks

## Pipeline

1. RepoMem.read — load long-term architecture and memory as task context
2. Superpowers.brainstorming — clarify vague intent
3. RepoMem.capture — open task-level temporary docs (requirement, architecture)
4. Superpowers.writing-plans — produce executable plan
5. Superpowers.using-git-worktrees + executing-plans + TDD — implement
6. RepoMem.capture (continuous) — record tacit knowledge into temporary memory
7. Superpowers.verification-before-completion — verify behavior
8. Superpowers.requesting-code-review
9. Superpowers.finishing-a-development-branch
10. RepoMem.merge (HITL) — promote stable knowledge from temporary to long-term
11. RepoMem.prune / split — periodic hygiene, not per-task

## Cross-Layer Conflicts

| Overlap | Conflict | Resolution |
|---|---|---|
| Plan vs memory | Superpowers.writing-plans vs RepoMem temporary requirement doc | writing-plans defines HOW; RepoMem temporary doc records WHY/context; never duplicate plan steps into memory |
| Verification vs memory | Superpowers.verification-before-completion vs RepoMem.merge | verification gates branch completion; RepoMem.merge runs after branch finishing, never as part of verification |
| Doc sedimentation | task-level temporary memory vs long-term memory | temporary memory is authoritative during the task; long-term memory only accepts content via the HITL merge step |

## Verification Topology

- Superpowers.verification-before-completion is the single verification entry before requesting-code-review.
- RepoMem has no verification role — it does not gate completion.

## Merge Gates

- RepoMem.merge MUST run after Superpowers.finishing-a-development-branch, never before.
- RepoMem.merge is HITL — a human reviews promotion candidates before they enter the long-term layer.
- RepoMem.prune / split runs on a different cadence than per-task work.

## Execution Policy

| # | Step | Policy | Skip Criteria (auto-judge only) | Notes |
|---|---|---|---|---|
| 1 | RepoMem.read | auto | — | Read-only context load. Idempotent. |
| 2 | Superpowers.brainstorming | auto-judge | Intent is already `clear` per task contract, or task is a trivial fix / pure refactor. | When skipped, writing-plans consumes the user prompt directly. |
| 3 | RepoMem.capture (open temp docs) | auto | — | Mechanical folder creation. |
| 4 | Superpowers.writing-plans | auto-judge | Single-step task with no branching, or `<3h test` delivery target. | When skipped, agent executes a 1-2 line ad-hoc plan. |
| 5 | using-git-worktrees + executing-plans + TDD | auto-judge | No isolation needed; small change with no parallel work. TDD scope follows plan. | Worktree decision is judged; TDD discipline is not. |
| 6 | RepoMem.capture (continuous) | auto | — | Passive tacit-knowledge recording. |
| 7 | Superpowers.verification-before-completion | auto | — | Behavior verification is non-negotiable. |
| 8 | Superpowers.requesting-code-review | ask-first | — | External visibility: opens a review request. Confirm before triggering. |
| 9 | Superpowers.finishing-a-development-branch | ask-first | — | Multi-step git state change. Confirm scope before executing. |
| 10 | RepoMem.merge | HITL | — | Contract-mandated human review. Never downgradable. |
| 11 | RepoMem.prune / split | auto | — | Periodic hygiene, not per-task. Runs on its own cadence. |

## Suitability Envelope

### Fits

- one active contributor maintaining a platform/library repository
- long-lived repositories where architecture and decisions accumulate
- changes that do not yet need explicit spec boundaries
- risk low to medium

### Does Not Fit

- short-lived or disposable repositories — use `superpowers` instead
- changes that need durable spec boundaries for external consumers — extend to `openspec-superpowers-repomem`
- multi-team large-scale coordination — needs a heavier primary workflow

## Temporary Contractor Boundary Rules

### Temporary Contractor May Adjust

- Task-level workflow composition
- Task-level intensity of spec, review, and verification
- Task-level temporary additions or skips inside the allowed baseline
- add `OpenSpec` for a single task that needs an explicit change boundary
- raise or lower `Superpowers` intensity
- enable lightweight `ECC` for a task
- skip `RepoMem.capture` for trivial `<3h test` tasks (still must read long-term memory)

### Temporary Contractor May Not Override

- Long-term repository memory existence
- Long-term quality and safety hard constraints
- Long-term default governance decisions unless this document is fully rewritten
- Pipeline ordering, Merge Gates, and Verification Topology are recipe-level invariants. The temporary contractor MAY skip one of them only by declaring the exception in its `Recipe Invariant Exceptions` section, with reason and compensating action. Default is deny — silent skips are forbidden.

## Full Rewrite Conditions

- Collaboration scale changes materially (solo → team)
- Repository type changes materially (platform → research/product/other)
- Repository horizon flips to short-lived
- The default workflow stack changes at the long-term level

(Note: switching from `superpowers-repomem` to a superset recipe such as `openspec-superpowers-repomem` is an extension under add-only — update `Recipe Reference` in place; this is not a full rewrite.)

## Related Documents

- `docs/HarnessStack/temporary-<task>.md`
- `docs/RepoMem/persist/version-plan[.{lang}].md`
- Related RepoMem architecture and memory docs

## Cross-Method Invariants

These rules bind HarnessStack to its companion methods. They are not part of any single layer and therefore live here at the long-term contract level.

- **Single task identifier.** When the active recipe includes both OpenSpec (Spec Layer) and RepoMem (Repository Memory Layer), the OpenSpec `change-id`, the RepoMem `<slug>`, and the HarnessStack `<task>` MUST be the same string: `<task>` = `change-id` = `<slug>`. Diverging on naming defeats per-task cross-referencing across the three method layers. (In the current recipe OpenSpec is inactive; the rule still applies the moment a task adopts OpenSpec via the temporary contract.)
- **No content duplication across per-task document sets.** OpenSpec change docs (`openspec/changes/<id>/`), RepoMem temp docs (`docs/RepoMem/temp/<slug>/`), and the HarnessStack per-task contract (`docs/HarnessStack/temporary-<task>.md`) each answer a distinct question and MUST NOT restate each other's content. The reviewer at `RepoMem.merge` rejects (deletes, does not merge) any temp content that duplicates the archived OpenSpec record.
- **Multi-session work uses sendbox-protocol.** When a task involves multiple sessions, dispatched subagents, or cross-worktree coordination, the `sendbox-protocol` skill (see `skills/sendbox-protocol/SKILL.md`) is the canonical asynchronous channel. The main agent's `docs/sendbox/` is the single source of truth, and subagents in side cwds write to it by relative or absolute path — never fan out under their own cwd. See `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md` for the embedding playbook (relevant when this contract is mirrored into other HS-managed repos).
- Divergence from any of the above requires a `Recipe Invariant Exception` in the task contract with reason and compensating action.
