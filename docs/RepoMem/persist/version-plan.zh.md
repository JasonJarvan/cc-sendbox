---
domain: version-plan
last_reviewed_at: 2026-05-15
---

# version-plan

## 当前定位

cc-sendbox 是 `sendbox-protocol` skill 的发布仓。HarnessStack 身份：**solo / platform / long-lived**，active recipe = `superpowers-repomem`。定位 = **个人 / 团队复用的 multi-agent 通信协议参考**，从 EverClaw 4 天实战（11+ agents / 22+ letter types）提炼而来。

目标用户：
- 在多个 worktree / session 编排 agent 工作的个人开发者
- 想要一套现成的"信件"约定而不必从零设计的小团队

非目标：
- 不是 multi-agent 框架（运行时由 Claude Code / 用户脚本自行决定）
- 不是 sendbox 的"实现"——纯协议文档
- 不打算 cover 所有 agent 通信形态（如实时 IPC、消息队列），只覆盖**文件级异步**这一窄但常用的场景

## v0.1 目标 / 待办

**核心目标**：让一个 outsider 拿到 SKILL.md 就能在自己的项目跑起来。

| Item | 状态 |
|---|---|
| SKILL.md 主体（10 节，按 EverClaw handoff §5 结构）| ✅ |
| TDD-for-docs 自测（RED → GREEN 一轮）| ✅ |
| 5 处 GREEN 修补（reachability test / anti-pattern #1 scope / 短角色别名 / cross-worktree default / frontmatter 字段语义）| ✅ |
| CLAUDE.md（仓库 type=skill，layers=Superpowers+RepoMem）| ✅ |
| RepoMem persist 初始化（本 version-plan + memory 沉淀）| 🟡 in progress |
| README.md | 🟡 next |
| Skill 可加载（symlink 到 ~/.claude/skills/）| 🟡 next |
| git init + 首次 commit | 🟡 next |

v0.1 收敛门 = 上述全部完成 + skill 在另一项目实测过一次 Quick Start 6 步。

**v0.1.0 released 2026-05-16** —— 收敛门用 cc-sendbox 自身的 dogfood 闭合（在本仓启用 sendbox 写第一封 letter + HS 嵌入设计 brainstorm + HITL merge）。See CHANGELOG.md.

## v0.2 in progress (2026-05-16 ~)

按 cost-aware 排序执行：

1. ✅ **Version + CHANGELOG** —— SKILL frontmatter `version: 0.1.0` + CHANGELOG.md (current task)
2. 🟡 **Test fixtures (manual runner)** —— `tests/outsider-scenarios/*.md`，~23 题固化；无自动 hook，控制成本
3. 🟡 **toUser glossary** —— 第一个 toUser letter 实例，含术语表
4. 🟡 **Sendbox letter back to EverClaw** —— 跨仓 letter，3 条 lesson 反哺到 `everclaw/docs/sendbox/toTuiOrchestrator/`

候选未排程：
- **打包为 Claude Code plugin**——加 plugin manifest。Trigger: 有第三方用户问"怎么装"。Test fixtures 是其前置条件之一
- **PR 到上游 Superpowers**——成为 `superpowers:sendbox-protocol`。Trigger: skill 经过 2-3 个真实项目实测、措辞稳定
- **Examples 目录**——加 `examples/letters/` 展示典型信件骨架（脱敏过的）。Trigger: 用户反馈 SKILL.md 文字描述不够具象。**部分由 toUser glossary 间接覆盖**
- **加 validator 脚本**——一个小 Python 校验器，检查 sendbox/ 目录是否合规（frontmatter / 命名 / lifecycle stale）。Trigger: dogfood 中发现手动审计太费时；ECC 层在此时正式进入

## 更远（方向性）

- 与其他多 agent 通信约定（MCP、A2A 等）的对照表
- 内化到 EverClaw 等 product 仓的"Local override"机制（允许 product 仓在本 skill 基础上加自家术语 / 角色名）

## 未来考虑（待验证）

- 现 SKILL.md ~1964 词，是否对 frequently-loaded skill 过重？需观察实际使用中是否经常 load 到上下文。若高频 load，考虑拆 `references/` 重载到子文件。
- 是否需要区分"orchestrator 写的 letter"vs"implementer 写的 letter"的检查清单？目前 SKILL.md 只按 letter type 区分。
- `created_in` 字段是否需要正式 schema？现 v0.1 是"人类可读标签"。若 plugin 化后想自动校验，需要更严格。
