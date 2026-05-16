> from: cc-sendbox main agent @ 2026-05-16
> recipient: User (you)
> purpose: persistent glossary of terms the skill and its surrounding docs use, with one-line definitions plus pointers to authoritative source
> lifecycle: persist (updated in place when terms evolve or new ones land)

# Glossary

A user-facing reference. Skim once; come back when you encounter a term in a letter, the skill, the playbook, or a commit message and want a 1-line refresher.

## Core protocol terms

| Term | One-line definition | Authoritative source |
|---|---|---|
| **sendbox** | A directory (`docs/sendbox/`) holding asynchronous, file-based letters between agent sessions. | `SKILL.md` overview |
| **letter** | A single `.md` file under `docs/sendbox/to<Role>/` carrying a discrete piece of communication (handoff, decision, blocker, …). | `SKILL.md` directory layout |
| **role** | A function or position (e.g. "Orchestrator"), not a session name. Roles outlive sessions; that's why directories are `to<Role>/`. | `SKILL.md` core principle #1 |
| **session** | One running agent conversation; ends when the chat / process ends. | (general agent terminology) |
| **main agent** | The session whose cwd is the project root — typically the orchestrator on the main branch / main worktree. Holds the canonical `docs/sendbox/`. | `SKILL.md` core principle #0 |
| **canonical sendbox** | The one and only `docs/sendbox/` per project, held by the main agent. All letters end up here, even from subagents in other cwds. | `SKILL.md` core principle #0 |
| **subagent** | An agent spawned from another agent's session, typically to handle an isolated task in a different cwd / worktree. | (general agent terminology) |
| **fan-out** (anti-pattern) | Creating multiple `docs/sendbox/` folders across cwds. Forbidden — sendboxes do not fan out. | `SKILL.md` core principle #0 + Red flags |
| **cwd** | Current working directory of a session. Different sessions can have different cwds (different worktrees, different repos, `/tmp`). | (general OS terminology) |

## Letter types (canonical catalog)

| Term | Trigger | Source row in catalog |
|---|---|---|
| **handoff** | New session spawned; needs full entry context | `handoff.md` |
| **plan-ready** | Implementer finished planning; awaits greenlight | `from-<x>-plan-ready.md` |
| **greenlight** | Orchestrator approves a plan | `from-<orche>-<x>-greenlight.md` |
| **blocker** | A-12 stop-and-ask — agent can't or shouldn't proceed unilaterally | `from-<x>-blocker-<topic>.md` |
| **decisions (bundle)** | Orchestrator bundles N decisions to one recipient | `from-<orche>-<x>-decisions.md` |
| **milestone-done** | Vertical slice / acceptance gate hit | `from-<x>-<milestone>-done.md` |
| **archived** | Task complete, letter chain wrapping up | `from-<x>-archived.md` |
| **promote-to-durable** | Forced human review for promoting transient knowledge to a durable knowledge store | `from-<x>-promote-to-durable.md` |
| **broadcast** | New constraint applies to all active sessions | `toAllActiveSessions/from-<orche>.md` |
| **toUser** | Letters to a human reader: glossary, mid-task ask, persistent reference (you're reading one now) | `toUser/<topic>.md` |

## Patterns

| Term | What it means | Source |
|---|---|---|
| **A-12 stop-and-ask** | The discipline: when an agent reaches a boundary it cannot cross unilaterally (scope mismatch, destructive change, upstream ambiguity), it STOPS there, writes a blocker letter with 2-3 options, and waits for upstream / user decision. "A-12" is shorthand for this pattern. | `SKILL.md` Communication patterns |
| **multi-decision ack bundle** | When an orchestrator owes responses to N items at once, write ONE bundled letter with numbered decisions — not N letters. | `SKILL.md` Communication patterns |
| **escalation with options** | Present alternatives as `(a) preferred / (b) safer fallback / (c) defer-to-user`, with time + risk per option. Avoid asking "what should I do?" with no proposed answers. | `SKILL.md` Communication patterns |
| **minimal sendbox** | Don't write letters for chat-synchronous decisions when the recipient will pick them up naturally. The #1 noise source. | `SKILL.md` Anti-patterns #1 |
| **reachability test** | The decisive question for minimal-sendbox: does the recipient see this chat? If they're in a detached worktree, sendbox is *delivery*, not redundancy. | `SKILL.md` When to use / Anti-patterns #1 |
| **dogfood** | Using the skill's own conventions inside the repo that ships the skill. cc-sendbox dogfoods sendbox-protocol by routing its own multi-doc workflow through `docs/sendbox/`. | (general engineering term) |

## Lifecycle terms

| Term | Meaning | Source |
|---|---|---|
| **lifecycle** | The condition under which a letter ceases to be active. Stated in the letter's frontmatter (multi-recipient) or quote block (single-recipient). | `SKILL.md` YAML frontmatter |
| **burn** | `git rm` the letter when its lifecycle ends. Default disposition. | `SKILL.md` YAML frontmatter |
| **archive** | Move the letter to `docs/sendbox/archive/` for historical reference. Used when the letter has audit value but is no longer active. | `SKILL.md` YAML frontmatter |
| **persist** | Promote the letter's content into durable docs (architecture / memory / spec), then burn the letter. Used when a letter contains a lesson worth keeping. | `SKILL.md` YAML frontmatter |
| **supersession rule** | A concrete trigger naming the event (date, milestone, or `superseded by <filename>`) that ends a broadcast's lifecycle. | `SKILL.md` YAML frontmatter |
| **rotting unburned letter** | A letter past its lifecycle that nobody burned. The #1 hygiene failure. | `SKILL.md` Anti-patterns #5 |

## Framework-side terms (appear in surrounding docs, not in the skill body)

| Term | Meaning | Source |
|---|---|---|
| **HarnessStack** | A modular development workflow framework. cc-sendbox itself runs on HarnessStack at `superpowers-repomem` recipe. | `docs/HarnessStack/longterm.md` |
| **recipe** | A named bundle of HarnessStack methods (e.g. `superpowers-repomem` = Superpowers + RepoMem; `openspec-superpowers-repomem-ecc` = +OpenSpec +ECC). | `docs/HarnessStack/longterm.md` |
| **cross-method invariant** | A rule that holds across multiple HarnessStack methods, declared once in the long-term contract. sendbox's positioning in HS is a cross-method invariant. | `docs/RepoMem/persist/architecture/sendbox-in-harnessstack.md` |
| **HarnessFactory** | An agent (or operator) that produces or upgrades a HarnessStack-managed repository. The audience of the embedding playbook. | `docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md` |
| **RepoMem** | The repository-memory method: structured architecture / memory / version-plan persistence with HITL merge. | `~/.claude/skills/repo-mem/SKILL.md` |
| **HITL merge** | Human-in-the-loop review before promoting transient temp knowledge to persistent architecture / memory. | `~/.claude/skills/repo-mem/references/merge-rules.md` |
| **temp doc** | Task-scoped working memory under `docs/RepoMem/temp/<slug>/`. Lives until promoted (HITL merge) or pruned. | `~/.claude/skills/repo-mem/SKILL.md` |
| **persist doc** | Long-term repository knowledge under `docs/RepoMem/persist/`. Authoritative; only changes via HITL merge or direct edit. | `~/.claude/skills/repo-mem/SKILL.md` |

## Pointers

- Skill body: [`skills/sendbox-protocol/SKILL.md`](../../../skills/sendbox-protocol/SKILL.md)
- Self-description for agents: [`../toAllAgents/from-sendbox-self-desc.md`](../toAllAgents/from-sendbox-self-desc.md)
- HS embedding playbook: [`../toHarnessFactory/from-sendbox-embedding-playbook.md`](../toHarnessFactory/from-sendbox-embedding-playbook.md)
- Architecture decision: [`../../RepoMem/persist/architecture/sendbox-in-harnessstack.md`](../../RepoMem/persist/architecture/sendbox-in-harnessstack.md)
- CHANGELOG: [`../../../CHANGELOG.md`](../../../CHANGELOG.md)
- GitHub: https://github.com/JasonJarvan/cc-sendbox

## Lifecycle of this glossary

`persist`. Updated in place when a new term enters the surrounding docs, when a definition evolves, or when a term is retired. Never burned unless the entire skill is renamed or superseded.
