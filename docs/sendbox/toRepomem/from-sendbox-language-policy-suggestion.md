> from: cc-sendbox main agent @ 2026-05-16
> recipient: repo-mem skill maintainer (whoever owns `~/.claude/skills/repo-mem/` upstream)
> purpose: propose an additive refinement to repo-mem's `references/language-policy.md` — HITL / non-HITL audience split with `.{lang}.md` filename suffix convention. Already locally-applied on this machine; this letter is the upstream-PR seed.
> lifecycle: burn after upstream repo-mem accepts the proposal (or explicitly rejects, or 60-day staleness)

# from-sendbox-language-policy-suggestion

## TL;DR

cc-sendbox formulated a language policy splitting docs by audience (HITL → user language, non-HITL → English) with a `.{lang}.md` filename suffix convention. We applied it locally to `~/.claude/skills/repo-mem/references/language-policy.md` as an **additive** new section (existing Primary/Secondary/Sync model untouched). This letter requests upstream repo-mem consider adopting the same refinement.

No urgency; advisory.

## Why this refinement complements repo-mem

repo-mem's current language-policy.md handles **translation-sync** (one primary language; secondary languages as mirrors stored under `multi-lang/<language>/`). That model answers: "if I have N language versions, which is authoritative and when do I sync?"

It does NOT answer: "should a given doc be written in English or my native language?"

The audience-based split (HITL vs non-HITL) answers exactly that. The two models are complementary:

| Question | Answered by |
|---|---|
| Which language is authoritative when there are multiple translations? | Primary/Secondary model (current) |
| What language should this specific doc be in? | Audience-based refinement (proposed) |
| When to translate? | Translation_sync_policy (current) |
| Filename convention to mark non-English content? | Suffix convention (proposed) |

## What was locally applied

The exact diff applied to `~/.claude/skills/repo-mem/references/language-policy.md` on this machine (2026-05-16):

- **Appended** a new section "Audience-Based Language Choice (optional refinement)" after the existing "Sync Policy" section
- Introduces two new configs: `userLanguage` (default = project primary language), `modelLanguage` (default = English)
- Specifies HITL/non-HITL rule: HITL docs → userLanguage; non-HITL docs → modelLanguage
- Adds filename suffix convention: `.{lang}.md` for non-English; no suffix = English
- Adds `persist/config.md` config keys: `audience_split: enabled|disabled` (default disabled for backwards compat), `user_language`, `model_language`
- Marks the proposed section as **optional refinement** — does NOT replace the Primary/Secondary model; the two coexist

Full proposed text is in the appended section of the local file. Reproducing it here would duplicate; see the file or the cc-sendbox-side mirror at [`../toAllAgents/from-sendbox-language-policy.md`](../toAllAgents/from-sendbox-language-policy.md).

## Why additive, not replacement

- Backwards compatibility: repos already using Primary/Secondary keep working with zero change (`audience_split` defaults to disabled)
- Opt-in path: repos with multilingual maintainers can enable the refinement without losing translation-sync semantics
- Lower bar for upstream acceptance: a strict additive change is easier to review than a behavioral replacement

## Suggested upstream action

Pick one:

| Option | Effort | Outcome |
|---|---|---|
| **(a)** Accept the proposed section verbatim into `language-policy.md` upstream | ~5 min review | Refinement available to all repo-mem users at next install |
| **(b)** Accept with revisions (rename keys / restructure / tighten wording) | 10-30 min | Variant of (a) with maintainer's editorial pass |
| **(c)** Reject with reason | minimal | Local users keep their own version; sister-skill letter retired |
| **(d)** Counter-propose a different abstraction (e.g. integrate into existing Primary/Secondary model rather than additive section) | discussion | Re-evaluate cc-sendbox local copy |

cc-sendbox has no strong preference between (a)/(b); both close the gap. (c) is fine — local users can fork their own version. (d) is welcome — upstream maintainer has authority over the abstraction shape.

## How to verify the refinement is usable

A repo wishing to test the refinement can:

1. Add `audience_split: enabled`, `user_language: <code>`, `model_language: en` to `persist/config.md`
2. Classify existing docs as HITL or non-HITL
3. Rename non-conforming files using the `.{lang}.md` suffix
4. New docs: classify before writing, choose language accordingly

cc-sendbox itself adopts this in its own `CLAUDE.md § Language policy`. Reading that section is the fastest concrete example.

## Lifecycle of this letter

- **burn** when one of:
  - upstream repo-mem maintainer accepts (a), (b), or (c) — letter has served its purpose
  - upstream repo-mem maintainer counter-proposes (d) and discussion converges to a stable solution
  - 60 days elapse with no upstream response — letter stale; cc-sendbox retains local copy and writes a fresh letter or moves on
- **on burn**: cc-sendbox CLAUDE.md § Language policy "Relationship to repo-mem language-policy" subsection updates accordingly

## Cross-references

- Local applied file: `~/.claude/skills/repo-mem/references/language-policy.md`
- cc-sendbox companion policy: [`../toAllAgents/from-sendbox-language-policy.md`](../toAllAgents/from-sendbox-language-policy.md)
- cc-sendbox dogfood: `CLAUDE.md § Language policy` (https://github.com/JasonJarvan/cc-sendbox/blob/main/CLAUDE.md)
- GitHub: https://github.com/JasonJarvan/cc-sendbox

## Author note

This letter is itself a sendbox letter authored from cc-sendbox to repo-mem (a sister skill). It demonstrates the inter-skill governance dialogue pattern: sister skills exchange recommendations via sendbox letters in the sender's repo, addressed to the recipient role. The recipient maintainer reads at their convenience and replies via their own channel (PR / issue / sister letter in their own repo if they adopt sendbox).
