# Multi-Agent 架构设计

> 状态: Active | 版本: v3.5 | 作者: MA Team
>
> **关联文档：**
> - 时序图: `sequence-diagram.md` — 全流程交互序列（todo.md和journey.md贯穿）
> - 执行 Skill: `skills/SKILL.md` — 团队研发操作流程
> - 部署指南: `SETUP.md` — 环境配置与初始化
> - 模板: `main/todo-template.md`, `main/journey-template.md`, `auditor/templates.md`, `tester/templates.md`

---

## 1. 总体架构

```
┌─────────────────────────────────────────────────────┐
│                      用户                            │
│                       │                              │
│                       ▼                              │
│  ┌──────────────────────────────────────────────┐   │
│  │              main (主控/总调)                  │   │
│  │  🎓 顾问 · 🏅 教练 · 📋 PM · 👥 HR · ✍️ 总编辑  │   │
│  │  模型: deepseek-v4-pro                        │   │
│  └───┬───────┬──────────┬────────────────────┘   │
│      │       │          │                         │
│      │  前置审计       │                         │
│      │       ▼          │                         │
│  ┌───┴──────────────┐   │                         │
│  │   auditor 🔒     │   │                         │
│  │  前置审计+终审    │   │                         │
│  │  模型: flash      │   │                         │
│  └───────┬──────────┘   │                         │
│          │ 通过          │                         │
│          ▼              │                         │
│  ┌───────────┐          │                         │
│  │  coder    │          │                         │
│  │ 设计+编码  │          │                         │
│  │ 模型: pro  │          │                         │
│  └─────┬─────┘          │                         │
│        │                │                         │
│        ▼                │                         │
│  ┌──────────┐           │                         │
│  │  tester  │           │                         │
│  │ 测试     │           │                         │
│  │ 模型:flash│           │                         │
│  └─────┬────┘           │                         │
│        │ 测试通过        │                         │
│        ▼                │                         │
│  ┌──────────────┐       │                         │
│  │ auditor 🔒   │       │                         │
│  │ 交付终审      │       │                         │
│  │              │       │                         │
│  │ 有问题? ─────┼───────┼──→ main 决策            │
│  │ 通过? ───────┼───────┼──→ main ✅ → 用户       │
│  └──────────────┘       │                         │
└─────────────────────────────────────────────────────┘

⚠️ auditor 发现问题 → main 决策（不直接指挥 coder/tester）
```

**核心流程：** `需求 → auditor前置审计 → coder → tester → auditor终审 → main ✅ → 用户`

**全流程时序详见：** `sequence-diagram.md`

---

## 2. Agent 设计

### 2.1 main — 主控

| 属性 | 值 |
|------|-----|
| **agentId** | `main`（默认 agent） |
| **模型** | `deepseek/deepseek-v4-pro` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace` |

**五重身份：** 顾问（需求分析）→ 教练（质量把关）→ PM（进度通报）→ HR（任务调度）→ 总编辑（文档审阅）

**详细行为准则：** `main/AGENTS.md` | **人格定义：** `main/SOUL.md`

### 2.2 coder — 技术实现

| 属性 | 值 |
|------|-----|
| **agentId** | `coder` |
| **模型** | `deepseek/deepseek-v4-pro` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-coder` |

**双重身份：** 架构师（设计先行）→ 编码人员（实现交付）

**子 Agent 模式（maxSpawnDepth: 2）：** 复杂需求时 spawn 子 agent 做设计，再 spawn 子 agent 做实现。

**详细行为准则：** `coder/AGENTS.md` | **人格定义：** `coder/SOUL.md`

### 2.3 tester — 测试

| 属性 | 值 |
|------|-----|
| **agentId** | `tester` |
| **模型** | `deepseek/deepseek-v4-flash` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-tester` |

**职责：** 测试设计 → 执行 → Bug报告（与coder直接交互，上限3轮）→ 回归验证 → 提交审计

**缺陷报告模板：** `tester/templates.md` | **详细行为准则：** `tester/AGENTS.md`

### 2.4 auditor — 审计

| 属性 | 值 |
|------|-----|
| **agentId** | `auditor` |
| **模型** | `deepseek/deepseek-v4-flash` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-auditor` |

**三重审计：** 完成情况检查 + 流程合规审计 + 安全审计（前置审计含 clarify 歧义识别，终审含 checklist 质量清单）

**问题管理模板：** `auditor/templates.md` | **详细行为准则：** `auditor/AGENTS.md`

### 2.5 publicist — 写手

| 属性 | 值 |
|------|-----|
| **agentId** | `publicist` |
| **模型** | `deepseek/deepseek-v4-pro` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-publicist` |

**职责：** 技术文档、散文随笔、润色修改。只与 main 通信。

**详细行为准则：** `publicist/AGENTS.md` | **人格定义：** `publicist/SOUL.md`

---

## 3. 通信矩阵

```
        ┌────────┬────────┬────────┬─────────┬───────────┐
        │  main  │ coder  │ tester │ auditor │ publicist │
────────┼────────┼────────┼────────┼─────────┼───────────┤
 main   │   -    │  ↔️     │  ↔️     │  ↔️      │    ↔️      │
 coder  │  ↔️     │   -    │  ↔️     │  ↔️      │    —      │
 tester │  ↔️     │  ↔️     │   -    │  ↔️      │    —      │
auditor │  ↔️     │  ↔️     │  ↔️     │   -     │    —      │
publicist│ ↔️    │   —    │   —    │   —     │    -      │
────────┴────────┴────────┴────────┴─────────┴───────────┘

publicist 只与 main 通信（写作需求唯一来源）
其余 agent 全部互通
```

**关键交互规则：**
- main ↔ 所有 agent（调度中心）
- coder ↔ tester（bug 修复闭环，上限 3 轮）
- auditor 发现问题 → main 决策（不直接指挥 coder/tester）
- tester → main → auditor（审计通过 main 转手）

---

## 4. 三文档体系

| 文档 | 本质 | 谁写 | 生命周期 |
|------|------|------|---------|
| **todo.md** | 任务看板（WBS） | 5个agent | 阶段0→项目结束 |
| **journey.md** | 过程日志 | 5个agent | 阶段0→项目结束 |
| **checklist.md** | 质量验证清单 | auditor | 阶段8 |

**模板：** `main/todo-template.md`, `main/journey-template.md`

**详细说明：** `skills/SKILL.md` §三件套

---

## 5. 项目目录规范

```
project/
├── docs/                   ← 所有文档集中管理
│   ├── constitution.md     ← main 产出，项目宪章（L级）
│   ├── spec.md             ← main 产出，需求规格
│   ├── design.md           ← coder 产出，架构设计
│   ├── style-guide.md      ← main 产出，代码风格
│   ├── test-report.md      ← tester 产出，测试报告
│   ├── audit-report.md     ← auditor 产出，审计报告
│   ├── audit-issues.md     ← auditor 产出，问题登记追踪
│   ├── bugs.md             ← tester 产出，缺陷登记追踪
│   ├── checklist.md        ← auditor 产出，质量验证清单
│   ├── todo.md             ← 各 agent 维护，任务看板
│   └── journey.md          ← 各 agent 写入，过程日志
├── src/                    ← 源代码
├── tests/                  ← 测试文件
├── scripts/                ← 构建/运行脚本
├── README.md               ← publicist 产出
├── CHANGELOG.md            ← 版本变更记录
├── CONTRIBUTING.md         ← 贡献指南
└── .gitignore
```

按语言裁剪 src/tests 子目录结构，详见 `SETUP.md`。

---

## 6. 配置设计

### 6.1 Agent 配置参考（YAML）

```yaml
agents:
  defaults:
    subagents:
      maxSpawnDepth: 2
      maxChildrenPerAgent: 5
      runTimeoutSeconds: 1800

  list:
    - id: "main"
      default: true
      workspace: "~/.openclaw/workspace"
      model: "deepseek/deepseek-v4-pro"

    - id: "coder"
      workspace: "~/.openclaw/workspace-coder"
      model: "deepseek/deepseek-v4-pro"

    - id: "tester"
      workspace: "~/.openclaw/workspace-tester"
      model: "deepseek/deepseek-v4-flash"

    - id: "auditor"
      workspace: "~/.openclaw/workspace-auditor"
      model: "deepseek/deepseek-v4-flash"

    - id: "publicist"
      workspace: "~/.openclaw/workspace-publicist"
      model: "deepseek/deepseek-v4-pro"

tools:
  agentToAgent:
    enabled: true
    allow: ["main", "coder", "tester", "auditor", "publicist"]

bindings:
  - agentId: "main"
    match:
      channel: "*"
      accountId: "*"
```

### 6.2 Projects 软链接

每个 agent workspace 必须有 `projects` 软链接指向 main workspace：

```bash
for agent in coder tester auditor publicist; do
  ln -s ~/.openclaw/workspace/projects ~/.openclaw/workspace-$agent/projects
done
```

**这是新机器部署的 #1 陷阱。** 详见 `SETUP.md`。

---

## 7. 已知限制

### 7.1 Agent 间通信：main 转手模式

tester 完成测试 → sessions_send → main → main sessions_spawn auditor。每次转手约 30-60 秒延迟，不影响正确性。

### 7.2 模板同步

`projects/ma/` 是模板仓库，agent workspace 是部署目标。修改模板后 main 需通过融合机制同步（详见 `SETUP.md` §初始化）。

### 7.3 模型成本

相比单 agent 增加 2-3x token 消耗。auditor 只在 tester 通过后触发，不会空转。
