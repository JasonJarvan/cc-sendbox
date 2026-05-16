---
recipients:
  - role: any agent reader or repo maintainer adopting sendbox-protocol
    purpose: governance recommendation on documentation language separation; not a sendbox protocol requirement, but a coherent companion practice
    lifecycle: persist (canonical policy doc; updates supersede in place)
on_lifecycle_end: persist
created: 2026-05-16
created_in: cc-sendbox main agent
---

# Language policy — companion recommendation for sendbox-protocol adopters

This is a **governance recommendation**, not a `sendbox-protocol` skill requirement. The skill body stays framework-agnostic and language-agnostic; this letter documents the documentation-language convention used in cc-sendbox itself, which adopters may choose to inherit.

## Why this is separate from the skill

`sendbox-protocol` defines *how letters are written* (paths, frontmatter, lifecycle). It does NOT define *what language they are written in*. Letter authors in any language follow the protocol identically.

Language is a **documentation governance** concern. Mixing it into the protocol body would (a) violate the customer-routing principle (skill stays portable across user languages), and (b) make the skill less reusable for users with different language preferences.

## Core proposal

Two-tier language model:

| Config | Default | Meaning |
|---|---|---|
| `userLanguage` | (empty; ask user if not set) | Language the human maintainer reads / interacts with |
| `modelLanguage` | English | Language for model-facing internal documentation |

### Rules

1. **HITL / user-facing docs** (user reads them in workflow — explicit interaction expected): write in `userLanguage`
2. **Non-HITL / model-facing docs** (agents read them; user typically does not touch in workflow): write in `modelLanguage` = English
3. **External discovery docs** (project README, public landing): English by default for international reach; localized translations land at `.{lang}.md` siblings

### Filename suffix convention

- `<name>.md` (no suffix) = English (default; matches modelLanguage)
- `<name>.{lang}.md` = non-English content (ISO 639-1: `.zh`, `.ja`, `.de`, …)

When `userLanguage ≠ English`, a user-facing doc MUST carry the suffix.

## Why this works

- **Cost-efficient**: models perform better in English, English is more compact in tokens; model-facing docs benefit
- **User-respectful**: user reads HITL docs in their own language; no friction
- **Cross-team interop**: model-facing docs are mutually intelligible across teams using different `userLanguage` values
- **Mechanical / enforceable**: filename suffix is grep-able; non-conforming files easy to audit
- **Reversible**: a doc's language can be flipped without semantic change (just translation + rename)

## Classification example (typical adopter)

| Doc category | Audience | Language | Filename pattern |
|---|---|---|---|
| Project README | external | English (often) | `README.md`; `README.{lang}.md` translation optional |
| Long-term contract (`longterm.md`, etc.) | model | English | `*.md` |
| Skill body (`SKILL.md`) | model | English | `*.md` |
| Architecture / memory docs | model | English | `*.md` |
| Sendbox letters to other agents (`toAllAgents/`, `toHarnessFactory/`, etc.) | model | English | `*.md` |
| Sendbox letters to user (`toUser/`) | user | userLanguage | `*.{lang}.md` if non-English |
| Glossaries / mid-task asks for user | user | userLanguage | `*.{lang}.md` if non-English |
| Public-facing release notes | external | English | `*.md` |

## Adoption guidance

If you adopt sendbox-protocol in your repo and want to mirror this policy:

1. Decide your `userLanguage` (ask the human if unset)
2. Add a "Language policy" section to your repo's `CLAUDE.md` (or equivalent always-loaded instruction file) declaring `userLanguage`, `modelLanguage`, and the rules above
3. Audit existing docs: rename non-English files to `.{lang}.md`; rewrite HITL docs into `userLanguage` on next substantive edit (do not force-rewrite solely for compliance)
4. New docs: classify before writing; pick language + suffix accordingly

## Relationship to sister-skill governance

`repo-mem` skill ships its own `language-policy.md` at `~/.claude/skills/repo-mem/references/language-policy.md`. That mechanism predates this letter; it is more focused on translation-sync (primary vs mirror) than on HITL/non-HITL classification.

cc-sendbox's recommendation here is **parallel-coexisting** with repo-mem's, per project decision 2026-05-16. A sister-skill letter at [`../toRepomem/from-sendbox-language-policy-suggestion.md`](../toRepomem/from-sendbox-language-policy-suggestion.md) (when authored) proposes repo-mem incorporate the HITL/non-HITL split as a refinement.

Adopters may choose to follow either policy, both, or neither. There is no enforcement layer; both policies are advisory governance.

## This letter's own lifecycle

`persist` — updated in place when rules evolve. Never burned unless the language policy itself is retracted or fully superseded by a stronger governance mechanism (e.g. an upstream HarnessStack cross-method invariant).

## Pointers

- cc-sendbox's own application: [`../../../CLAUDE.md`](../../../CLAUDE.md) § Language policy
- Sister-skill letter: [`../toRepomem/from-sendbox-language-policy-suggestion.md`](../toRepomem/from-sendbox-language-policy-suggestion.md)
- repo-mem's existing policy: `~/.claude/skills/repo-mem/references/language-policy.md`
- GitHub: https://github.com/JasonJarvan/cc-sendbox
