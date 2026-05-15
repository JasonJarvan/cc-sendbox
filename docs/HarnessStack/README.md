# HarnessStack — `cc-sendbox` at solo/long-lived

> One-time distillation source for an AI agent operating in the target
> repository. Read once, condense sections 1–4 into `CLAUDE.md`, then
> consult `longterm.md` for full detail. Re-read this file only on recipe
> upgrade.

## 1. Identity

This repository runs HarnessStack at `solo` scale, horizon `long-lived`.

- **Active recipe:** `superpowers-repomem`
- **Source template:** `harness-factory/assets/templates/longterm/solo.md`
- **Effective from:** 2026-05-15

## 2. Active Methods

- `Superpowers` — execution discipline (brainstorming, writing-plans, using-git-worktrees, executing-plans, TDD, verification-before-completion, requesting-code-review, finishing-a-development-branch).
- `RepoMem` — long-term repository memory (persist + temp split, HITL merge).
- Spec Layer — **inactive** by default; a task may add `OpenSpec` via its temporary contract when an explicit change boundary is required.
- Primary Workflow Layer — **inactive** by default; primary workflow responsibility is emergent, carried by Superpowers brainstorming and writing-plans.
- Harness Enhancement Layer — **inactive** by default; add `ECC(light)` only when memory, hooks, or verification loops would benefit.

## 3. Per-Task Pipeline (Compressed)

> This is the compressed view. **It is not authoritative.** For the full
> step list, conflict rules, and exception handling, see
> `longterm.md § Pipeline`.

1. `RepoMem.read` — load long-term architecture and memory as task context.
2. `Superpowers.brainstorming` — clarify vague intent.
3. `RepoMem.capture` — open task-level temporary docs (requirement, architecture).
4. `Superpowers.writing-plans` — produce executable plan.
5. Execute: `using-git-worktrees` + `executing-plans` + TDD; `RepoMem.capture` continuous.
6. `Superpowers.verification-before-completion` — single verification entry.
7. `Superpowers.requesting-code-review` — external visibility; ask-first.
8. `Superpowers.finishing-a-development-branch` — ask-first.
9. `RepoMem.merge` (HITL) — promote stable knowledge from temporary to long-term.
10. `RepoMem.prune / split` — periodic hygiene, not per-task.

## 4. Hard Invariants

- **Single task identifier (conditional).** When a task adopts `OpenSpec` via its temporary contract, `<task>` = `change-id` = `<slug>` across HarnessStack, OpenSpec, and RepoMem docs. With OpenSpec inactive, the RepoMem `<slug>` and HarnessStack `<task>` MUST still match.
- **Add-only.** A method that has been activated never deactivates; upgrades are extensions (superset recipe), never replacements.
- **Single verification gate.** `Superpowers.verification-before-completion` MUST pass before `finishing-a-development-branch`. RepoMem does not gate completion.
- **Merge ordering.** `RepoMem.merge` runs only after `finishing-a-development-branch`, never before — and is HITL.
- **No content duplication across per-task document sets.** RepoMem temp docs and `temporary-<task>.md` answer distinct questions and MUST NOT restate each other. `RepoMem.merge` rejects duplicated content.

## 5. Where To Read More

- Full long-term contract: `./longterm.md`
- Task-level patch (if a task is in progress): `./temporary-<task>.md`

## 6. For AI Consumers

Distill sections 1–4 into `CLAUDE.md` (or the equivalent always-loaded
instruction file in your harness). Do not duplicate `longterm.md` verbatim;
the contract remains the authoritative source for details. Re-read this
README only on recipe upgrade or full rewrite events.
