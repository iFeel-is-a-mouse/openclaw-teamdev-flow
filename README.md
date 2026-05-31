# MA — Multi-Agent 开发团队

> 一支由 AI Agent 组成的软件开发团队。各司其职，相互协作，质量把关，交付可靠。

---

## 团队构成

| Agent | 角色 | 一句话职责 |
|-------|------|-----------|
| **main** 🎓🏅📋👥✍️ | 顾问·教练·PM·HR·总编辑 | 用户的唯一接口，需求入口，结果出口，团队调度 |
| **coder** 🏗️💻 | 架构师·编码 | 设计技术方案，编写高质量代码 |
| **tester** 🧪 | 测试 | 黑盒+白盒测试，保证代码质量 |
| **auditor** 🔒 | 审计·守门员 | 需求前置审计 + 交付终审，质量最后一道闸 |
| **publicist** ✍️ | 写手 | 技术文档、散文随笔、润色修改 |

## 工作流

```
用户提需求
    │
    ▼
0 main 复杂度评估+流程声明
    │
    ▼
1 main 需求分析 → auditor 前置审计+clarify ──→ main 把关
                                                    │
                                                    ▼
                                              4 coder 架构设计
                                                    │
                                                    ▼
                                              5 analyze 一致性检查
                                                    │
                                                    ▼
                                              6 coder 编码实现
                                                    │
                                                    ▼
                                              7 tester 测试
                                                    │
                                                    ▼
                                              8 auditor 终审+checklist
                                               │         │
                                           有问题     ✅ 通过
                                               │         │
                                               ▼         ▼
                                          main 决策    main ✅
                                          迭代修复    → merge
                                                        │
                                                        ▼
                                              9 main README初稿
                                                        │
                                                        ▼
                                              10 publicist 文档定稿
                                                        │
                                                        ▼
                                              11 复盘（按需）
```

> 核心链路：`需求 → 前置审计 → 设计 → 编码 → 测试 → 终审 → merge → 文档`

## 快速开始

### 1. 下载

```bash
git clone https://github.com/iFeel-is-a-mouse/team-dev.git
```

### 2. 创建 Agent

在 OpenClaw 中创建 5 个 agent：

```bash
openclaw agents create coder    --workspace ~/.openclaw/workspace-coder
openclaw agents create tester   --workspace ~/.openclaw/workspace-tester
openclaw agents create auditor  --workspace ~/.openclaw/workspace-auditor
openclaw agents create publicist --workspace ~/.openclaw/workspace-publicist
```

main agent 即你的默认 agent，无需额外创建。

创建 projects 软链接（所有 agent 共享同一目录）：

```bash
for agent in coder tester auditor publicist; do
  ln -s ~/.openclaw/workspace/projects ~/.openclaw/workspace-$agent/projects
done
```

### 3. 初始化

告诉 main：

> 学习 `SETUP.md`，为 coder、tester、auditor、publicist 融合同步各自的 AGENTS.md 和 SOUL.md，让他们准备好团队研发。

main 会读取各角色模板，通过 `sessions_send` 发送给对应 agent，**融合（而非覆盖）**写入各 agent 的 workspace。详见 `SETUP.md`。

### 4. 验证配置

告诉 main：

> 团队研发：验证配置，检查各 agent 是否就绪。

main 会逐一确认 agent 可达、软链接正确、模板已同步。全部通过后即可开始。

### 5. 开始开发

对 main 说出你的需求：

> 团队研发：帮我开发一个 XXX 功能

main 会自动评估复杂度、分配合适的流程（S/M/L 三级），调度整个团队协作完成。

## 关键设计决策

1. **Auditor 覆盖完整生命周期** — 前置审计确保需求质量，终审确保交付质量
2. **文件系统是通信总线** — 所有 agent 共享 `projects/` 目录，零配置，零延迟
3. **宪法先行** — 项目启动先定 constitution.md，所有后续决策以此为最高依据
4. **深度任务用 Pro，常规任务用 Flash** — main/coder/publicist 用深度模型，tester/auditor 用快速模型
5. **信任但验证** — main 相信 agent 的能力，但每次关键产出都要审查

## 文档导航

```
team-dev/
├── README.md                  ← 你在这里
├── SETUP.md                   ← 理解、部署与配置指南
├── CHANGELOG.md               ← 版本变更记录
├── VERSION                    ← 当前版本号
├── multi-agent-design.md      ← 架构设计：Agent设计、通信矩阵、三文档体系、配置参考
├── sequence-diagram.md        ← 时序图：全流程交互序列，todo.md和journey.md贯穿全程
├── RETROSPECTIVE.md           ← 复盘报告
├── skills/
│   └── SKILL.md               ← 团队研发执行流程（核心 Skill）
├── main/                      ← main agent 配置
│   ├── AGENTS.md              ← 团队研发行为准则
│   ├── SOUL.md                ← 五重身份人格
│   ├── constitution-template.md ← 项目宪章模板
│   ├── todo-template.md       ← 项目任务看板模板
│   └── journey-template.md    ← 项目过程日志模板
├── coder/
│   ├── AGENTS.md              ← 编码规范、设计层次
│   └── SOUL.md                ← 工匠精神
├── tester/
│   ├── AGENTS.md              ← 测试方法论
│   ├── SOUL.md                ← 职业怀疑者
│   └── test-report-template.md ← 缺陷管理 + 测试报告模板
├── auditor/
│   ├── AGENTS.md              ← 三重审计+checklist
│   ├── SOUL.md                ← 公正法官
│   └── audit-report-template.md ← 问题管理 + 审计报告模板
└── publicist/
    ├── AGENTS.md              ← 写作规矩
    └── SOUL.md                ← 书卷气、文采
```

---

*"信任是好的，审计更好。" — auditor 格言*
