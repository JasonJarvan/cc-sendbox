> from: cc-sendbox main agent @ 2026-05-16
> recipient: 用户（你）
> purpose: v0.2 批次工作收尾提醒 + 列出仍待处理事项，让你或未来 session 接手时不漏事
> lifecycle: burn 在用户读完且决定了所有事项的处理路径之后；或 60 天 stale 后

# v0.2 批次工作收尾 — 用户提醒清单

## 已发布版本

- ✅ **v0.1.0**（2026-05-16）—— 协议主体；GitHub tag `v0.1.0` 已推
- ✅ **v0.2.0**（2026-05-16）—— handoff 两种模式（child vs inheritance）；tag `v0.2.0`
- ✅ **v0.2.1**（2026-05-16）—— cleanup checkpoints（session end / task convergence 主动清理）；tag `v0.2.1`

CHANGELOG.md 是详细变更记录入口。Tags 都在 GitHub 上：<https://github.com/JasonJarvan/cc-sendbox/tags>

## 本批次新沉淀的 RepoMem persist

| 路径 | 内容 |
|---|---|
| `architecture/sendbox-in-harnessstack.md` | sendbox 在 HS 中作为 cross-method invariant 的定位 + customer-routing 原则（HITL merge 已落） |
| `memory/handoff-modes.md` | child vs inheritance 模式区分的设计 rationale + 失败模式 |
| `memory/canonical-sendbox-and-main-agent.zh.md` | main agent 定位 + 跨 cwd 写规则（v0.1 沉淀） |
| `memory/minimal-sendbox-scope.zh.md` | reachability 测试规则（v0.1 沉淀） |

## 仍待处理事项（按优先级）

### A. 你 EverClaw orchestrator session 决定 — `from-sendbox-creator-v01-promotion-suggestions.md` 命运

- 位置：`/home/zhaosheng/Codes/everclaw/docs/sendbox/toTuiOrchestrator/from-sendbox-creator-v01-promotion-suggestions.md`
- 状态：untracked，在 EverClaw repo 里等 orchestrator session 处理
- 内容：cc-sendbox 给 EverClaw 的 3 条 lesson 反哺建议（main-agent rule / fan-out anti-pattern / customer-routing）+ 3 个 option（accept diff / 安装 skill / 暂搁置）
- 行动选择：
  - 切到 EverClaw orchestrator session，决定 accept / 安装 / 搁置
  - 决定后再 commit / burn 这封 letter

### B. 词汇表中文化 — `glossary.zh.md`

- 当前：`docs/sendbox/toUser/glossary.md` 是英文写的
- 政策：toUser = HITL = userLanguage = 中文，应该是 `glossary.zh.md`
- 状态：在 CLAUDE.md § Language policy 标 "scheduled rewrite"
- 行动选择：
  - 下次实质性编辑词汇表时一并迁移；不为合规单独做
  - 或现在就翻译一份（成本 ~30 分钟）

### C. repo-mem 上游 PR（toRepomem letter 已写好种子）

- 本机已 apply：`~/.claude/skills/repo-mem/references/language-policy.md` 加了 "Audience-Based Language Choice" 节
- 风险：本机改动；如果重装 / 升级 repo-mem skill 可能被覆盖
- 已备 PR 种子：`docs/sendbox/toRepomem/from-sendbox-language-policy-suggestion.md`
- 行动选择：
  - 找到 repo-mem 上游仓地址（如果有 GitHub 仓），手动 PR
  - 或不 PR，本机用着

### D. 未排程 v0.2 候选（version-plan.zh.md 内列）

- **Plugin manifest**（让别人 `git+install` 直接拿）—— 高价值外部分发条件
- **Examples 目录**（脱敏 letter 骨架示例）—— 部分被 toUser glossary 间接覆盖
- **Validator 脚本**（Python 校验器查 sendbox/ 合规）—— dogfood 加速工具
- **PR 上游 Superpowers**（成为 `superpowers:sendbox-protocol`）—— 需 skill 跑过 2-3 个真实项目稳定后

行动选择：
- 你想推外部分发 → plugin manifest 优先
- 你想自己用得舒服 → validator 脚本
- 你想加深野外验证 → 在另一个真实项目试 Quick Start
- 都不急 → 留版本规划，下次再挑

## 本 session 完成清单（参考）

- v0.1.0 ship + 3 commits（base + dogfood + cross-method invariant codify）
- v0.2.0 ship + 1 commit（handoff modes）
- v0.2.1 ship + 1 commit（cleanup checkpoints）
- 27 个 outsider fixture 场景（6 个 fixture 文件）+ manual runner README
- 5 封 sendbox letter（self-desc / language-policy / embedding-playbook / repomem-suggestion / glossary）
- Language policy 落地 + 3 个中文文件 rename `.zh.md`
- repo-mem 本机 global apply（Audience-Based Language Choice）
- README.zh.md（中文用户友好版）
- HITL merge 1 次（sendbox-in-harnessstack temp → persist/architecture）
- 3 个 git tag 推到 GitHub

## 本 letter 自己的 lifecycle

`burn` —— 当你读完且决定了 §A/§B/§C/§D 的处理路径之后，或 60 天后 stale。下次 session 启动时如果它还在，说明上面还有未决事。

---

🎯 v0.2 批次收敛。你休息 / 切别的事 / 继续推任何 §A-§D 都行。
