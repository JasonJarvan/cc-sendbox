---
domain: sendbox-protocol
last_reviewed_at: 2026-05-15
---

# Canonical sendbox + main-agent 定位

## 规则

每个项目**唯一一个** canonical `docs/sendbox/`，由 **main agent**（cwd = project root 的 session，通常是 main 分支 / main worktree 上的 orchestrator）持有。其他任何 cwd 的 subagent（不同 worktree、不同仓、`/tmp` 临时 dir）写信都要**目标到 main agent 的 sendbox**（相对 traversal 或绝对路径），不允许在自己 cwd 下另起一个 `docs/sendbox/`。

## Why（v0.1 self-test 后用户指出的 gap）

v0.1 self-test GREEN 通过后，user review 发现 SKILL.md 两个 gap：

1. **"main agent" 定位完全缺失** —— grep 全无。skill 提到 orchestrator / implementer / subagent 但没说"谁持有 canonical sendbox"
2. **跨 cwd 写信规则不完整** —— 原 "Cross-worktree write" 节只覆盖"subagent in same-repo `.worktrees/`"一种情况，没覆盖"subagent 完全在另一个仓 / `/tmp`"

不补这两条，多 agent 编排会出现 sendbox **fan-out**——subagent 在自己 cwd 各自起 `docs/sendbox/`，结果 main agent 看不见，user 也找不到 letter。

## GREEN 修补（4 处 SKILL.md edit）

1. **Core principles** 加 #0：定义 main agent + canonical sendbox + "never fan out"
2. **Cross-worktree write** 改名 **Cross-cwd write**，扩成 3-case 表（same-repo worktree / 不同 repo or /tmp / 同 cwd 平级）
3. **Hard rules** 节加 3 条：no fan-out / no dual-write / unreachable 则 surface 给 user 不要写 shadow sendbox
4. **Quick start step 1** 强调"先挑 main agent's project root，只在那里 mkdir"
5. **Red flags** 加一条：subagent cwd 下出现 `docs/sendbox/` 即误用

## GREEN 验证（fresh outsider）

派第二个 fresh subagent 跑 3-case + bonus 定义题，3/3 全对，bonus 定义精准。GREEN 闭合。

## How to apply

派 subagent 写 prompt 时显式声明：
- "Main agent canonical sendbox lives at `<absolute project root>/docs/sendbox/`"
- "If you write a letter, write it there by relative or absolute path; do NOT create `docs/sendbox/` in your own cwd"

main agent 自己启动时：
- 检查 `docs/sendbox/` 是否已存在（first-time 才需要 mkdir 那 3 个 default 子目录）
- 在跨仓派遣场景（如 EverClaw → cc-sendbox dispatch）显式给 subagent **main agent 的绝对路径**

## 反例

- ❌ subagent 在 `.worktrees/feature-x/` 写 `.worktrees/feature-x/docs/sendbox/` （fan-out 反例）
- ❌ subagent 在跨仓时新建 `<other-repo>/docs/sendbox/` 来"放自己的 letter"
- ❌ dual-write 主 + sub 两侧（必然忘 push 一侧）

## 正例

- ✅ same-repo worktree subagent → 相对 `../../docs/sendbox/to<Role>/...` 或 commit on branch
- ✅ 跨仓 subagent → 绝对路径 `/home/.../<main-project>/docs/sendbox/to<Role>/...`
- ✅ unreachable → 不写信，直接 surface 给 user

## 关联

- SKILL.md "Core principles" #0 → main agent 定义
- SKILL.md "Cross-cwd write" 节 → 3-case 表
- SKILL.md "Red flags" → fan-out 自检
- [[minimal-sendbox-scope]] —— 都是 self-test 沉淀的"规则边界澄清"系列
- [[version-plan]] —— 本 memory 加在 v0.1 收敛门后续 patch 序列
