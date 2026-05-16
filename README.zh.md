# cc-sendbox

让多个 AI agent session 异步通信的 Claude Code skill。

> 🇬🇧 English version: [`README.md`](README.md)
> 📦 GitHub: <https://github.com/JasonJarvan/cc-sendbox>

## 这是什么 / 为什么需要

当你用 Claude Code 同时运行多个 agent session（例如：一个 orchestrator 派 subagent 去独立 worktree 干活；或者跨 repo 协调任务），它们之间的对话不能用 chat 完成 —— **chat 只跟当前 session 说话**，无法穿透 worktree、不同 cwd、或 session 重启。

`sendbox-protocol` 提供一套 **文件协议**，agents 之间通过 markdown letter 异步通信。letter 进 git 仓库，跨 session / 跨 worktree / 跨 repo / 跨重启都活着。Letter 有生命周期（要么 burn 销毁，要么 archive 归档，要么 persist 沉淀进文档），不会无限累积。

## 它给你什么

- **目录约定**：`docs/sendbox/to<角色>/` (按角色寻址，不绑 session 名)
- **文件命名**：`handoff.md`（入场信）+ `from-<发件人>-<主题>.md`（其他）
- **YAML frontmatter 规范**（多收件人必需）
- **10 种标准 letter 类型**：handoff、plan-ready、greenlight、blocker、decisions、milestone-done、archived、promote-to-durable、broadcast、toUser
- **2 种 handoff 模式**：child-handoff（子任务）vs inheritance-handoff（角色交接）
- **5 种沟通模式**：含 A-12 stop-and-ask（停下来问一下再继续）
- **7 个反模式**：实战踩坑总结
- **完整 lifecycle**：burn / archive / persist + 主动清理检查点（session 结束时 / 任务收敛时）
- **27 个测试场景** 防回归

完整内容见 [`skills/sendbox-protocol/SKILL.md`](skills/sendbox-protocol/SKILL.md)（英文 —— 给 AI agent 看的，框架无关）。

## 安装（Claude Code）

把 skill 软链接到你的个人 skills 目录：

```bash
mkdir -p ~/.claude/skills
ln -s "$(pwd)/skills/sendbox-protocol" ~/.claude/skills/sendbox-protocol
```

下次启动 Claude Code 时它就会被识别。Claude 在合适场景会自动通过 `Skill` 工具调用 `sendbox-protocol`。

未来 v0.3 计划提供 plugin 形式安装。

## 什么时候用

会用得上：
- 你派 subagent 到独立 worktree / 不同 cwd / 另一个 repo 干活
- agent 需要"停下来问你一下"再继续（A-12 stop-and-ask 模式）
- 决策 / 阻塞 / 里程碑必须留痕，超过当前 chat 生命周期
- 主控 agent 需要给所有 active session 广播新约束
- 旧 session 即将退出，把全部责任交接给一个继任者 session

什么时候**不用**：
- 决策能在 chat 里同步说完，且所有 session 都看得到
- 内容应该进永久文档（架构 / 规范 / memory）而不是 transient letter
- 两个 session 可以实时配对编程

## 安装到你自己的项目

如果你想把 sendbox 协议嵌入到你的项目（HarnessStack 仓 / 任意 Claude Code 项目），步骤详见 [`docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md`](docs/sendbox/toHarnessFactory/from-sendbox-embedding-playbook.md)（英文嵌入剧本，给"工厂 agent"或仓库维护者看）。

剧本含：
- 决策树（你的仓什么情况下需要 sendbox）
- 嵌入清单（装 skill / 加 1 行 invariant / 验证）
- 反模式（什么时候 NOT 嵌入）
- 复制粘贴用的 invariant 模板

## 仓库结构

```
cc-sendbox/
├── CLAUDE.md                              # 仓库规则（HarnessStack 配置 / 语言政策 / etc）
├── README.md                              # 英文版（外部访客 / GitHub 首页）
├── README.zh.md                           # 本文件
├── LICENSE                                # MIT
├── CHANGELOG.md                           # skill 版本历史
├── skills/
│   └── sendbox-protocol/
│       └── SKILL.md                       # 主体协议（英文，给 AI agent 读）
├── docs/
│   ├── HarnessStack/                      # HarnessStack 长期契约
│   ├── RepoMem/persist/                   # 长期知识沉淀
│   │   ├── version-plan.zh.md             # 中文路线图
│   │   ├── architecture/                  # 架构决策（含 sendbox-in-harnessstack）
│   │   └── memory/                        # 跨任务 lesson（部分中文 .zh.md）
│   └── sendbox/                           # cc-sendbox 自己的 sendbox（dogfood）
│       ├── toAllAgents/                   # 给所有 agent 读者的信
│       ├── toHarnessFactory/              # 给 HS 工厂 agent 的嵌入剧本
│       ├── toRepomem/                     # 给 repo-mem 姊妹 skill 的建议信
│       └── toUser/                        # 给用户的信（含术语表）
└── tests/
    └── outsider-scenarios/                # 27 个 fixture 场景（任何 SKILL 改动前手动跑一遍防回归）
```

## 状态

- **当前版本**：v0.2.1（2026-05-16）
- 0.2.1 新增：cleanup checkpoints —— session 结束 / 任务收敛时主动清理 letter
- 0.2.0 新增：handoff 两种模式（child vs inheritance）
- 0.1.0 初版：协议主体 + main agent 定位 + 跨 cwd 写规则 + framework-agnostic Interop
- 详细路线图见 [`docs/RepoMem/persist/version-plan.zh.md`](docs/RepoMem/persist/version-plan.zh.md)
- 完整变更记录见 [`CHANGELOG.md`](CHANGELOG.md)

## 词汇表

如果你在用本仓时遇到不懂的术语（A-12 / main agent / canonical sendbox / fan-out / lifecycle / handoff / blocker 等），看 [`docs/sendbox/toUser/glossary.md`](docs/sendbox/toUser/glossary.md)（目前英文，未来会出中文版）。

## 出处

`sendbox-protocol` 提炼自 EverClaw 项目 2026 年 4 天 multi-agent sprint（11+ agents、22+ letter type、8 sendbox 目录）。EverClaw 的业务术语（agent 名 EVE-14/15/16、内部项目词汇）在 skill 里被刻意去掉了 —— 只保留可复用的模式。

## 协议

MIT —— 见 [`LICENSE`](LICENSE)。
