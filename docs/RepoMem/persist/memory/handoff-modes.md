---
domain: sendbox-protocol
last_reviewed_at: 2026-05-16
---

# Handoff has two modes: child vs inheritance

The `handoff.md` letter type is semantically overloaded. There are two distinct relationships it captures, with different content requirements. Treat them as two modes, not one type.

## Rule

| Mode | Relationship | Lifecycle |
|---|---|---|
| **child** | Parent persists; new session is a bounded subordinate that converges back | Parent stays alive; child reports back via `milestone-done` or `blocker`; both letters burn after convergence |
| **inheritance** | Parent terminates; new session takes ownership of parent's full responsibility | Parent dies after writing handoff; inheritor has no one to report back to; handoff letter typically persists until inheritor's first milestone-done |

Plus a non-mode: **peer dialogue** is not a handoff — use normal letter types (`from-<x>-<topic>.md`) between coexisting sessions.

## Why this distinction matters (the failure mode)

Without the mode split, agents over-pack OR under-pack handoff letters:

- **Over-packing a child handoff**: dumping full version-plan + architecture + reading list into a brief for a 30-minute subtask. Wastes child's context window; scope-creep risk; child reads things it doesn't need.
- **Under-packing an inheritance handoff**: treating role transfer like a subtask brief — minimal context, no status snapshot. Inheritor lacks the situational awareness to take over and writes a "what is my actual scope?" blocker letter back to a parent that no longer exists. Recovery is expensive.

Both failure modes are observed in cc-sendbox / EverClaw practice. EverClaw's 270-line `from-tuiorche-handoff.md` (an inheritance handoff) packs the right content for that mode; a child-handoff in the same format would be massive over-engineering.

## How to apply

When about to write `handoff.md`, run the mode detection rule (SKILL.md):

1. "Does the spawning session continue to live after this handoff?" → yes ⇒ child; no ⇒ inheritance
2. "Does the new session need to report back?" → yes ⇒ child; no ⇒ inheritance

Both questions should agree. If they disagree (e.g. parent stays alive but new session has no convergence path), the relationship is ambiguous — clarify scope with the spawning agent before writing.

Declare mode in the letter body via top-line quote block:

```
> mode: child
```

or

```
> mode: inheritance
```

Filename stays `handoff.md` either way — mode is content metadata, not filename metadata.

## Content delta per mode

See SKILL.md "Handoff modes" section for the canonical content tables. Key reminders:

- **Child**: minimum scope; convergence path required; explicit out-of-scope statement; parent's cwd if cross-cwd
- **Inheritance**: full status snapshot; reading list; env state; pending decisions; **no convergence path** (forbidden — parent is gone)

## Provenance

- Pattern observed across EverClaw 4-day sprint (multiple successor handoffs as orchestrator sessions cycled) and cc-sendbox's own development
- Codified into SKILL.md 0.2.0 (commit pending at time of writing)
- Test coverage: `tests/outsider-scenarios/06-handoff-modes.md` (4 scenarios)

## Sister knowledge

- [[canonical-sendbox-and-main-agent]] — defines main agent (the spawning session of any child handoff is typically the main agent; the inheritor of an inheritance handoff becomes the new main agent)
- [[minimal-sendbox-scope]] — reachability rule (independent concern; applies to any letter, not just handoffs)

## Re-evaluation triggers

Revisit if:

- A third semantic relationship emerges (e.g. "fork-handoff" where parent and child both continue with overlapping scope) — would refute the binary mode split
- Real practice shows mode disagreement (parent persists + no report back) is common — would mean a third mode exists in practice
- Inheritance handoffs prove cheap enough to also serve as the parent-side audit log indefinitely — would change lifecycle from "burn after first milestone" to "persist forever"
