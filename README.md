# cc-sendbox

> 🇨🇳 中文版本: [`README.zh.md`](README.zh.md)

A portable convention for **asynchronous, file-based communication between agent sessions**.

Ships one skill — `sendbox-protocol` — distilled from a 4-day multi-agent sprint (11+ agents, 22+ letter types, 8 sendbox directories) into a reusable reference.

## What it gives you

- Directory layout: `docs/sendbox/to<Role>/`
- Letter naming: `handoff.md`, `from-<sender>-<topic>.md`
- 10 canonical letter types (handoff, plan-ready, greenlight, blocker, decisions, milestone-done, archived, repomem-merge-promotes, broadcast, toUser)
- YAML frontmatter spec for multi-recipient letters
- Lifecycle rules (burn / archive / persist)
- 5 communication patterns including the A-12 **stop-and-ask** flow
- 7 anti-patterns from real-world misuse
- 6-step Quick Start

Full content: [`skills/sendbox-protocol/SKILL.md`](skills/sendbox-protocol/SKILL.md).

## Install (Claude Code)

Symlink the skill into your personal skills directory:

```bash
mkdir -p ~/.claude/skills
ln -s "$(pwd)/skills/sendbox-protocol" ~/.claude/skills/sendbox-protocol
```

Claude Code picks the skill up at next session start. Invoke via the `Skill` tool with name `sendbox-protocol`.

## When to use the skill

Trigger the skill when you face any of:

- Coordinating one orchestrator session with 2+ implementer sessions in separate worktrees
- An agent needs to pause and request a decision before proceeding
- Decisions, blockers, or milestones must outlive the current chat session
- Broadcasting a new constraint to all active sessions

Skip the skill when chat coordination is sufficient — see SKILL.md §"When to use" / Do NOT use cases.

## Repository structure

```
cc-sendbox/
├── CLAUDE.md                              # repo-level rules (skill repo, light recipe)
├── README.md                              # you are here
├── skills/
│   └── sendbox-protocol/
│       └── SKILL.md                       # main artifact (~2k words)
└── docs/
    └── RepoMem/
        └── persist/
            ├── version-plan.zh.md         # v0.1 state + direction (currently 中文; see CLAUDE.md Language policy)
            └── memory/                    # cross-task lessons
```

## Status

- **v0.1** — protocol distilled + self-tested (RED→GREEN with fresh outsider subagents).
- Repository type: long-lived platform repo, solo scale; active recipe `superpowers-repomem` (see `docs/HarnessStack/longterm.md`).
- Direction: see `docs/RepoMem/persist/version-plan.zh.md`.

## Provenance

Distilled from the `everclaw` repo's `docs/HarnessStack/longterm.md § Cross-Session Sendbox Convention`. Business identifiers from the source project are intentionally absent — only the reusable pattern survives here.

## License

MIT — see [`LICENSE`](LICENSE).
