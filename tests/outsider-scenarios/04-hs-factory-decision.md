# Fixture 04 — HS factory embed/skip decision

Tests whether a HS-factory reader correctly applies the playbook §2 decision tree (recipe × scale × work-mode) across 4 concrete scenarios.

Covers: `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md` §2 decision tree.

Expected pass: **4 / 4**.

**Pre-read for subagent**: `docs/sendbox/toAllAgents/from-sendbox-self-desc.md` + `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md`. Subagent should NOT need to read SKILL.md to decide these (decisions are at the playbook layer).

## Prompt

You are a HarnessFactory agent producing or upgrading a new HS-managed repo. For each scenario, decide YES (embed sendbox) or NO (skip), and cite the specific playbook rule.

### S1
Target repo: solo developer. Recipe `superpowers` (no RepoMem). Build target: a tiny CLI for personal use. Single-session work always.

### S2
Target repo: small team (3 devs). Recipe `openspec-superpowers-repomem-ecc`. Multi-task sprints with subagents dispatched to worktrees. Currently 2 worktrees active.

### S3
Target repo: solo developer. Recipe `superpowers-repomem`. Long-lived platform repo. The developer confirms "this is always single-session, I never dispatch subagents."

### S4
Target repo: solo developer. Recipe `superpowers-repomem`. Long-lived platform repo. The developer says "I plan to start dispatching subagents to worktrees for the next big refactor."

## Expected

| # | Answer | Required citation |
|---|---|---|
| S1 | **NO** | Playbook §2.1 `superpowers` (no RepoMem) row: "Skip by default — single-session execution discipline; sendbox adds ceremony". §2.2 also fails (truly solo single-session). |
| S2 | **YES** | §2.1 `openspec-superpowers-repomem-ecc` row: "Embed by default". §2.2 satisfied by active multi-worktree dispatch (first OR second bullet). |
| S3 | **NO** | §2.1 `superpowers-repomem` row says "Embed iff §2.2 also says yes". §2.2 fails (truly solo single-session). §2.2 closing rule: "skip the embed even if §2.1 says embed-by-default." |
| S4 | **YES** | §2.1 `superpowers-repomem` row allows embed if §2.2 holds. §2.2 first bullet (subagent in different cwd/worktree plausible) AND second bullet (multi-worktree parallel work plausible) → embed. |

## Pass criteria

- Each decision (YES / NO) matches expected
- Citation must reference the playbook §2.1 row by recipe name AND §2.2 work-mode reasoning where applicable
- S3 is the trickiest — agent must combine "§2.1 says embed-by-default" with "§2.2 closing rule overrides §2.1 to skip" — both halves required for full pass
- Generic answers like "depends" without applying the decision tree explicitly = fail
