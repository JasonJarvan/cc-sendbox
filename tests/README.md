# Outsider regression scenarios

23 historical scenarios extracted from v0.1 RED→GREEN testing rounds, grouped by capability. Manual runner — there is no automated hook (kept that way until skill change frequency justifies it).

## When to run

Before any of:
- A non-trivial edit to `skills/sendbox-protocol/SKILL.md`
- A version bump (MINOR or MAJOR per SemVer)
- A release / packaging step (plugin manifest, PR upstream)

Skip for: typo / wording polish / pointer fixes / changelog-only edits.

## How to run (manual runner protocol)

For each fixture file in `tests/outsider-scenarios/`:

1. Open the file. Note the `## Prompt` section and `## Expected` section.
2. Dispatch a fresh `general-purpose` subagent with the following message verbatim, substituting `<FIXTURE-FILE>` with the prompt section ONLY (cut at the `## Expected` heading — the subagent must not see expected answers):

   > You are a fresh agent. Read only this file: `/home/zhaosheng/Codes/cc-sendbox/skills/sendbox-protocol/SKILL.md`. Do not read anything else, do not search. Then answer the scenarios below.
   >
   > `<FIXTURE-FILE>`

3. Compare the subagent's output to the `## Expected` section. Each scenario passes iff:
   - Decision (yes/no, path, type, etc.) matches expected
   - Subagent cites the specific rule from SKILL.md that is named in expected (citation does not need to be verbatim; semantic match is enough)
4. Tally pass rate. Target: **23/23**. Any miss is a regression — investigate before merging the change that caused it.

## Fixture files

| File | Capability tested | Scenarios | Covers SKILL.md sections |
|---|---|---|---|
| `01-letter-decision.md` | When to write a letter vs skip | 6 | When to use / Anti-patterns #1 / Reachability test |
| `02-letter-type-choice.md` | Picking the right letter type | 5 | Letter type catalog / Communication patterns |
| `03-cross-cwd-write.md` | Where the letter goes (path) | 6 | Core Principle #0 / Cross-cwd write / Red flags |
| `04-hs-factory-decision.md` | HS-factory embed/skip decision | 4 | Playbook §2 decision tree (out-of-skill) |
| `05-hs-factory-procedure.md` | HS-factory embedding mechanics | 2 | Playbook §3 checklist (out-of-skill) |
| `06-handoff-modes.md` | child vs inheritance vs peer | 4 | Handoff modes section + Mode detection rule (added in 0.2.0) |

Fixtures `01`–`03` and `06` test the skill itself; `04`–`05` test `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md`. Run `04`–`05` whenever the playbook changes (separate trigger from skill changes).

## Provenance

Scenarios collected from 4 subagent dispatches during 2026-05-15 → 2026-05-16:

| Source session | Original scenarios | Fixture mapping |
|---|---|---|
| v0.1 baseline (RED) | E1–E6 + T1–T3 (9) | 01 (E1,T1,T2,T3) + 02 (E2–E6) |
| v0.1 GREEN re-test | Q1–Q4 (4) | 01 (Q1,Q2) + 03 (Q3,Q4) |
| canonical-sendbox/main-agent GREEN | Q1–Q3 + Bonus (4) | 03 |
| HS-factory playbook GREEN | S1–S4 + P1–P2 (6) | 04 + 05 |

Total v0.1: 23. Added in 0.2.0: 4 (fixture 06). Cumulative: **27**.
