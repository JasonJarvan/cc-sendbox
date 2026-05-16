---
recipients:
  - role: any agent reader (human or AI) trying to understand cc-sendbox / sendbox-protocol in <5 minutes
    purpose: quick-orient on what the repo ships, what its boundaries are, where to read more
    lifecycle: persist (canonical self-description; updates supersede in place)
on_lifecycle_end: persist
created: 2026-05-15
created_in: main agent in cc-sendbox repo root
---

# Self-description — `cc-sendbox` / `sendbox-protocol`

Authoritative artifact: [`skills/sendbox-protocol/SKILL.md`](../../../skills/sendbox-protocol/SKILL.md). This letter is a navigation index, not a re-statement. Framework-agnostic; for embedding into a HarnessStack repo specifically, see [`../toHarnessFactory/from-sendbox-embedding-playbook.md`](../toHarnessFactory/from-sendbox-embedding-playbook.md).

## 1. What this repo is

`cc-sendbox` is a long-lived platform repository (solo scale) whose single product is the `sendbox-protocol` Claude Code skill. The skill defines a portable convention for **asynchronous, file-based communication between agent sessions** — directory layout, letter naming, frontmatter spec, lifecycle (burn / archive / persist), and 10 canonical letter types.

Repo identity (per [`docs/HarnessStack/longterm.md`](../../HarnessStack/longterm.md)):
- recipe: `superpowers-repomem`
- type: platform / solo / long-lived
- active layers: Superpowers + RepoMem (OpenSpec / ECC default-inactive, opt-in per task)

## 2. What the skill is for

Triggers (from [SKILL.md §"When to use"](../../../skills/sendbox-protocol/SKILL.md)):
- One orchestrator session coordinates ≥2 implementer sessions (separate worktrees or branches)
- An agent must pause and ask before proceeding (A-12 stop-and-ask)
- Decisions / blockers / milestones must outlive the current chat session
- Subagent in a different cwd / repo / `/tmp` needs to hand artifacts back upstream
- Broadcast to all active sessions

Skip when:
- A clear, trivial decision was given in chat AND no detached agent needs to learn it AND no follow-up upstream
- Durable docs (architecture, spec, memory) are the right channel
- Two sessions can pair-program in real time

## 3. Boundaries (what sendbox is NOT)

- NOT a state tracker (use a dashboard / spec)
- NOT a long-term memory (durable knowledge stores are that)
- NOT a multi-agent runtime / IPC mechanism (transport is just the filesystem)
- NOT a substitute for chat when chat reaches all parties

## 4. Hard rules

| Rule | Why it matters |
|---|---|
| One canonical `docs/sendbox/` per project, held by **main agent** (cwd = project root) | Subagents in side cwds write here by relative or absolute path; sendboxes do not fan out |
| Directory naming `to<Role>/` (PascalCase, role-not-session); short aliases like `toAllAgents` allowed if consistent | Roles outlive sessions |
| Letter naming `from-<sender>-<topic>.md` (ASCII lowercase-kebab) or `handoff.md` for entry | Cross-shell / cross-worktree safety |
| Single recipient → frontmatter optional; multi recipient → frontmatter MANDATORY (`recipients[]`, `on_lifecycle_end`, `created`, `created_in`) | Lifecycle accountability |
| Lifecycle disposition explicit: `burn` (`git rm`) / `archive` / `persist` (promote to durable docs then burn) | Prevents rotting unburned letters |

## 5. Pointers

- Skill body: [`skills/sendbox-protocol/SKILL.md`](../../../skills/sendbox-protocol/SKILL.md)
- Repo rules: [`CLAUDE.md`](../../../CLAUDE.md)
- HarnessStack contract: [`docs/HarnessStack/longterm.md`](../../HarnessStack/longterm.md)
- Roadmap: [`docs/RepoMem/persist/version-plan.zh.md`](../../RepoMem/persist/version-plan.zh.md)
- Past gap closures (RED→GREEN history): [`docs/RepoMem/persist/memory/`](../../RepoMem/persist/memory/)
- Embedding into HarnessStack repos: [`../toHarnessFactory/from-sendbox-embedding-playbook.md`](../toHarnessFactory/from-sendbox-embedding-playbook.md)
- GitHub: https://github.com/JasonJarvan/cc-sendbox

## 6. This letter's own lifecycle

`persist` — this is the navigation index for agent readers. Updated in place when the skill body or boundaries materially change; never silently deleted. If the skill itself is renamed or replaced, this letter is rewritten or replaced.
