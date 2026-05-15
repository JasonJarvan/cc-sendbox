# CLAUDE.md — cc-sendbox

> Condensed from `docs/HarnessStack/README.md` §1–4. Authoritative contract: `docs/HarnessStack/longterm.md`. Re-read README on recipe upgrade.

## Repository purpose

`cc-sendbox` is a long-lived platform repository that ships the **`sendbox-protocol` skill** — a portable convention for asynchronous, file-based communication between agent sessions. Distilled from a 4-day multi-agent sprint (11+ agents, 22+ letter types). Future direction: optional packaging as a Claude Code plugin.

Primary artifact: `skills/sendbox-protocol/SKILL.md`.

## Identity

- **Active recipe**: `superpowers-repomem`
- **Collaboration scale**: solo
- **Repository type**: platform
- **Project horizon**: long-lived
- **Effective from**: 2026-05-15

## Active methods

- **Superpowers** — execution discipline: brainstorming, writing-plans, using-git-worktrees, executing-plans, TDD, verification-before-completion, requesting-code-review, finishing-a-development-branch. Use the relevant skill for the task at hand.
- **RepoMem** — long-term repository memory under `docs/RepoMem/` (persist + temp split, HITL merge). Skill repos accumulate design rationale and rejected alternatives over time; RepoMem gives those a structured home.

### Inactive layers (may opt in per task)

These are **default inactive** but can be enabled for a single task via a `docs/HarnessStack/temporary-<task>.md` contract that declares the exception:

- **OpenSpec** — opt in when a change needs a stable boundary for external consumers (public API change, public contract surface). Trigger examples: skill surface change that downstream users version-pin against.
- **ECC** / **ECC(light)** — opt in when memory, hooks, or verification loops would benefit from harness-level enforcement. Trigger examples: a task that introduces executable code with dependencies.
- **Primary workflow layer** (BMAD / gstack / GSD) — opt in only on full recipe upgrade, not per-task.

When opting in, the task contract MUST honor the cross-method invariants below.

## Per-task pipeline (compressed)

Compressed view; for the authoritative step list, conflict rules, and exception handling see `docs/HarnessStack/longterm.md § Pipeline`.

1. `RepoMem.read` — load architecture + memory as context (auto)
2. `Superpowers.brainstorming` — clarify vague intent (auto-judge: skip when intent already clear)
3. `RepoMem.capture` — open `docs/RepoMem/temp/<slug>/` (auto)
4. `Superpowers.writing-plans` — produce executable plan (auto-judge: skip on single-step or `<3h test` tasks)
5. `using-git-worktrees` + `executing-plans` + TDD (auto-judge for worktree; TDD discipline not skippable)
6. `RepoMem.capture` (continuous) — passive tacit-knowledge recording (auto)
7. `Superpowers.verification-before-completion` — single verification gate (auto; non-negotiable)
8. `Superpowers.requesting-code-review` — external visibility (ask-first)
9. `Superpowers.finishing-a-development-branch` — git state change (ask-first)
10. `RepoMem.merge` — HITL promotion of stable knowledge to long-term layer
11. `RepoMem.prune / split` — periodic hygiene, not per-task

## Hard invariants

- **Single verification gate.** `verification-before-completion` MUST pass before `finishing-a-development-branch`. RepoMem does not gate completion.
- **Merge ordering.** `RepoMem.merge` runs only after `finishing-a-development-branch`, never before — and is HITL.
- **Add-only.** Once a method is activated it never deactivates; upgrades are extensions to a superset recipe, never replacements.
- **No duplication across per-task doc sets.** RepoMem temp docs and `temporary-<task>.md` answer distinct questions; `RepoMem.merge` rejects duplicated content. If OpenSpec is opted in for a task, `<task>` = `change-id` = `<slug>`.

## Working conventions

### Skill changes follow TDD-for-docs

When editing `skills/*/SKILL.md`:
1. RED — pressure-test current text with a fresh subagent; document baseline rationalizations/gaps.
2. GREEN — surgical edits that close those specific gaps.
3. REFACTOR — re-test with another fresh subagent; verify the gap stays closed.

This is the TDD pipeline (step 5 above) specialized for documentation. Describe semantic changes in the commit body.

### Sendbox (dogfood, only when applicable)

If multiple sessions ever coordinate in this repo, follow `skills/sendbox-protocol/SKILL.md` literally — letters to `docs/sendbox/to<Role>/`. The vast majority of work here is solo; the dogfood path is for completeness, not routine use.

### Commit conventions

- Conventional-commit with a scope: `skill(sendbox-protocol): …`, `docs(claude): …`, `repomem: …`, `chore: …`.
- Reference the source decision in the body when traceable: `per chat decision YYYY-MM-DD: <one-line>`.
- Burn lifecycles get their own commit: `sendbox: burn <letter> (lifecycle ended)`.

### Filenames

ASCII / English / lowercase-kebab. No localized characters, no spaces. Applies to skill files, RepoMem files, sendbox letters, scripts.

## Directory layout (current + on demand)

```
cc-sendbox/
  CLAUDE.md                          # this file (condensed contract)
  README.md                          # external discovery
  docs/
    HarnessStack/
      README.md                      # distillation source
      longterm.md                    # authoritative contract
      temporary-<task>.md            # per-task patch (one at a time)
    RepoMem/
      persist/
        version-plan.md              # current + next direction
        architecture/<topic>.md      # frontmatter: domain, last_reviewed_at
        memory/<topic>.md            # frontmatter: domain, last_reviewed_at
      temp/<slug>/                   # transient capture during a task
    sendbox/to<Role>/                # only if multi-session work happens
  skills/
    sendbox-protocol/
      SKILL.md
```

Do not scaffold directories speculatively; create them when content demands.

## Skills likely to apply here

- `superpowers:writing-skills` — editing the sendbox skill (primary activity)
- `superpowers:test-driven-development` — RED/GREEN/REFACTOR for skill edits
- `superpowers:brainstorming` — before any non-trivial skill restructure
- `superpowers:writing-plans` — for multi-step changes
- `superpowers:requesting-code-review` — before merging non-trivial changes
- `superpowers:verification-before-completion` — before claiming a task done
- `superpowers:finishing-a-development-branch` — branch close-out

## What this repo is NOT

- Not a sandbox for arbitrary experimentation — scope is the sendbox skill and its immediate supports.
- Not a meta-framework for all multi-agent protocols — sister patterns belong in their own repos.
- Not a home for product / team artifacts — anything requiring multi-team coordination indicates a wrong-repo placement.

## Full rewrite triggers

`docs/HarnessStack/longterm.md § Full Rewrite Conditions` lists the canonical set. Do NOT patch this file in place if any of these applies:

- Collaboration scale changes (solo → team)
- Repository type changes (platform → research/product/other)
- Horizon flips to short-lived
- The default workflow stack changes at the long-term level

Adding a superset recipe (e.g. switching to `openspec-superpowers-repomem` when OpenSpec becomes a default-active layer rather than per-task opt-in) is an **extension**, not a rewrite — update Recipe Reference in place.

## Provenance

`sendbox-protocol` is distilled from a 4-day multi-agent sprint on the `everclaw` repo. The canonical EverClaw definition lives at `docs/HarnessStack/longterm.md § Cross-Session Sendbox Convention` in that repo and was the seed. EverClaw business identifiers (agent names, project terms) are intentionally absent from this skill — only the reusable pattern survives.
