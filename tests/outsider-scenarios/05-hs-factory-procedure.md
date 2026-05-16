# Fixture 05 — HS factory embedding procedure

Tests whether a HS-factory reader correctly applies the playbook §3 embedding checklist after deciding YES.

Covers: `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md` §3 + §4.

Expected pass: **2 / 2**.

**Pre-read for subagent**: `docs/sendbox/toAllAgents/from-sendbox-self-desc.md` + `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md`.

## Prompt

### S1
You decided YES on a target repo. What is the EXACT sequence of mechanical actions you take? List as a numbered checklist. Cite playbook section per step.

### S2
True or false: "After deciding YES, I should create empty `docs/sendbox/toOrchestrator/`, `docs/sendbox/toUser/` folders in the target repo as scaffolding." Cite rule.

## Expected

### S1 — required steps (order matters)

1. Pre-read self-desc (`../toAllAgents/from-sendbox-self-desc.md`) — playbook §1 prerequisite
2. Install skill via cheapest mechanism — symlink `cc-sendbox/skills/sendbox-protocol` into `~/.claude/skills/sendbox-protocol` (or plugin manifest when v0.2 ships one) — playbook §3.1
3. Verify Claude Code recognises `sendbox-protocol` at next session start — playbook §3.1
4. Paste the §5 invariant template **verbatim** as the last bullet under § Cross-Method Invariants in the target's `docs/HarnessStack/longterm.md` — playbook §3.2; adjust only the parenthetical path if vendored (§5)
5. Treat §3.3 pipeline↔letter map as **reference only** — do NOT insert new pipeline steps — playbook §3.3
6. Run §3.4 verification: target CLAUDE.md doesn't weaken the four hard rules; `docs/sendbox/` is NOT scaffolded; skill path reachable; invariant text verbatim — playbook §3.4

### S2 — answer

**FALSE.**

Citations required:
- Playbook §3.4: "Target's `docs/sendbox/` is NOT scaffolded yet — it appears the first time a letter is actually written"
- Playbook §4 anti-pattern #2: "Do not scaffold `docs/sendbox/` with empty subdirectories"

## Pass criteria

- S1 must list at least the install (§3.1), invariant-paste (§3.2), and verification (§3.4) steps. Missing any of those three = fail.
- S1 partial credit if pipeline-map (§3.3) and pre-read (§1) are omitted — those are softer.
- S1 must explicitly say "verbatim" or equivalent for the invariant template (the wording is part of the contract per playbook §5)
- S2 must say FALSE AND cite at least one of (§3.4 verification rule, §4 anti-pattern #2)
- A "TRUE" or "depends" answer for S2 = hard fail
