---
domain: sendbox-protocol
last_reviewed_at: 2026-05-15
---

# minimal-sendbox 规则的作用范围

## 规则

Minimal Sendbox 规则（SKILL.md anti-pattern #1）只**部分适用**于 orchestrator → detached implementer 的场景。需要按"reachability"判定。

## Why（从 self-test RED→GREEN 沉淀）

cc-sendbox v0.1 self-test 中，fresh outsider 在两个相似 case 给出了不同答案，靠 implicit triviality 推理补足：

- **E1**：user 在 chat 跟 orche 说"用 JWT 替代 sessions"，be 在 detached worktree 写 auth → outsider 写信 ✅
- **T1**：user 在 chat 跟 orche 说"`user_id` 改名为 `userId`"，be 同样 detached → outsider 不写信 ✅

两个 case 表面都满足"chat decision + 单一下游 affected"，但 outsider 凭直觉区分了**substantive directive**（JWT pivot 是设计层、需立即改方向）vs **trivial rename**（rebase 时自然可见）。

原 SKILL.md 措辞没区分二者，是真 gap。GREEN 阶段加了两条：

1. **Reachability test**（SKILL.md "When to use" 节）：detached recipient 看不到 chat —— sendbox 是 *delivery*，不是 redundancy。"skip the letter"豁免只在 recipient 会自然 pick up（chat 同席 OR rebase 前就会看到 commit）时生效。

2. **Anti-pattern #1 scope clause**：规则原本针对**worktree session reply upstream**（user 在 worktree 的 chat 答了，worktree 重复写信回主），不豁免**orchestrator relay substantive directive 给 detached implementer**。

## How to apply（未来 skill 改动时）

写 sendbox 相关规则时，**先问 reachability**：

- 接收方是否在 chat 里？→ 是：可不写信
- 接收方是否会因为 commit 自然看到决策（rename / trivial 改动会通过文件 diff 暴露）？→ 是：commit message cite 即可
- 接收方是否需要在做下一步前先收到方向（设计 pivot、blocker 解除）？→ 是：必须写信，trivial 与否都不影响

triviality 不是判定主轴 —— reachability + timing 才是。triviality 只是"会不会通过 commit 自然暴露"的次因。

## 反例（不要做）

- 把 minimal-sendbox 规则简化成"trivial 就不写信" —— 漏掉 detached agent 的 reachability 问题
- 把规则简化成"detached 就写信" —— 漏掉"会自然在 rebase 看到"的 trivial rename 类豁免
- 给所有 chat 决策都重复写信 —— 回到 #1 noise source（EVE-14 phase7-decisions 反例）

## 关联

- SKILL.md "When to use" 节 → Reachability test
- SKILL.md "Anti-patterns" #1 → scope clause
- [[version-plan]] v0.1 收敛清单 → 本 memory 是 v0.1 self-test 沉淀的核心 lesson
