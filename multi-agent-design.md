# Multi-Agent 架构设计

> 状态: Active | 版本: v3.6 | 作者: MA Team
>
> **关联文档：**
> - 时序图: `sequence-diagram.md` — 全流程交互序列（todo.md和journey.md贯穿）
> - 执行 Skill: `skills/SKILL.md` — 团队研发操作流程
> - 部署指南: `SETUP.md` — 环境配置与初始化
> - 模板: `main/todo-template.md`, `main/journey-template.md`, `auditor/audit-report-template.md`, `tester/test-report-template.md`

---

## 1. 总体架构

```
┌───────────────────────────────────────────────────────────────┐
│                           用户                                 │
│                            │                                   │
│                            ▼                                   │
│  ┌───────────────────────────────────────────────────────┐    │
│  │                  main (主控/总调)                       │    │
│  │   🎓 顾问 · 🏅 教练 · 📋 PM · 👥 HR · ✍️ 总编辑        │    │
│  │   模型: deepseek/deepseek-v4-pro                               │    │
│  └───┬───────┬──────────┬───────────────┬────────────┘    │
│      │       │          │               │                  │
│      │  前置审计       │               │                  │
│      │       ▼          │               │                  │
│  ┌───┴──────────────┐   │               │                  │
│  │   auditor 🔒     │   │               │                  │
│  │  前置审计+终审    │   │               │                  │
│  │  模型: deepseek/deepseek-v4-pro  │   │               │                  │
│  └───────┬──────────┘   │               │                  │
│          │ 通过          │               │                  │
│          ▼              │               │                  │
│  ┌──────────────────────────────┐       │                  │
│  │     结对编程 (Pair Prog)     │       │                  │
│  │  ┌──────────┐   ⟷   ┌──────┐│       │                  │
│  │  │  coder   │ 配对   │ code ││       │                  │
│  │  │  🖥️ 驾驶员  │══════╡ review││       │                  │
│  │  │ moonshot  │ 轮换   │ 🔍  ││       │                  │
│  │  └─────┬────┘   └──┬───┘│       │                  │
│  └────────┼────────────┼────┘       │                  │
│           │ 自测通过   │ 审查通过    │                  │
│           ▼            ▼            │                  │
│  ┌──────────┐                      │                  │
│  │  tester  │                      │                  │
│  │ 测试     │                      │                  │
│  │ 模型: deepseek/deepseek-v4-pro       │                  │
│        │ 测试通过        │               │                  │
│        ▼                │               │                  │
│  ┌──────────────┐       │               │                  │
│  │ auditor 🔒   │       │               │                  │
│  │ 交付终审      │       │               │                  │
│  │              │       │               │                  │
│  │ 有问题? ─────┼───────┼───────────────┼──→ main 决策     │
│  │ 通过? ───────┼───────┼───────────────┼──→ main ✅ → 用户│
│  └──────────────┘       │               │                  │
│                          │               │                  │
│                ┌─────────┴──┐            │                  │
│                │ publicist  │            │                  │
│                │ 写手 ✍️     │            │                  │
│                │ 模型: deepseek/deepseek-v4-pro   │            │                  │
│                └────────────┘            │                  │
└───────────────────────────────────────────────────────────────┘

⚠️ auditor 发现问题 → main 决策（不直接指挥 coder/tester）
⚠️ codereviewer 发现问题 → 直接与 coder 交互修复（上限 2 轮）→超出升级 main
```

**核心流程：** `需求 → auditor前置审计 → coder → codereviewer → tester → auditor终审 → main ✅ → 用户`

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

### 2.2 coder — 架构师 & 驾驶员

| 属性 | 值 |
|------|-----|
| **agentId** | `coder` |
| **模型** | `deepseek/deepseek-v4-pro` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-coder` |

**多重身份：**
- 🖥️ **驾驶员 (Driver)** — 编写代码实现，是结对编程中的"驾驶员"
- 🏗️ **架构师** — 设计先行，负责技术方案和系统架构
- 📊 **性能设计** — 确定项目性能指标（响应时间、吞吐量、并发数），工程师应在 design.md 中注明性能指标
- 🧭 **导航员** — 与 codereviewer 轮换角色，在审查对方代码时充当导航员

**结对编程：** 与 codereviewer 直接交互，轮换驾驶员/导航员角色。不同模型互补：coder (deepseek/deepseek-v4-pro) 擅长编码实现，codereviewer (zai/glm-5.1) 擅长逻辑审查。上限 3 轮，超出升级 main。

**子 Agent 模式（maxSpawnDepth: 2）：** 复杂需求时 spawn 子 agent 做设计，再 spawn 子 agent 做实现。

**详细行为准则：** `coder/AGENTS.md` | **人格定义：** `coder/SOUL.md`

### 2.3 tester — 测试

| 属性 | 值 |
|------|-----|
| **agentId** | `tester` |
| **模型** | `deepseek/deepseek-v4-pro` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-tester` |

**职责：** 测试设计 → 执行 → Bug报告（与coder直接交互，上限3轮）→ 回归验证 → 提交审计

**扩展职责：**
- **性能测试执行** — 执行性能测试方案，输出性能报告
- 性能标准由 auditor 制定，性能指标由 coder 设计指定

**缺陷报告模板：** `tester/test-report-template.md` | **详细行为准则：** `tester/AGENTS.md`

### 2.4 auditor — 审计

| 属性 | 值 |
|------|-----|
| **agentId** | `auditor` |
| **模型** | `deepseek/deepseek-v4-pro` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-auditor` |

**三重审计：** 完成情况检查 + 流程合规审计 + 性能标准制定
- **前置审计**（含 clarify 歧义识别）
- **终审**（含 checklist 质量清单，从代码审查转向审查的审查）
- **性能标准** — 制定项目性能测试标准，判定性能是否达标

**安全：** 安全审查不是当前最优先级，codereviewer 在结对编程中做基础安全扫描即可，auditor 不做专项安全审计。

**问题管理模板：** `auditor/audit-report-template.md` | **详细行为准则：** `auditor/AGENTS.md`

### 2.5 publicist — 写手

| 属性 | 值 |
|------|-----|
| **agentId** | `publicist` |
| **模型** | `deepseek/deepseek-v4-pro` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-publicist` |

**职责：** 技术文档、散文随笔、润色修改。只与 main 通信。

**详细行为准则：** `publicist/AGENTS.md` | **人格定义：** `publicist/SOUL.md`

### 2.6 codereviewer — 代码审查员 & 导航员

| 属性 | 值 |
|------|-----|
| **agentId** | `codereviewer` |
| **模型** | `zai/glm-5.1` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-codereviewer` |

**双重身份：**
- 🔍 **代码审查员** — 在 coder 自测完成后审查代码质量（功能、可读性、安全、性能）
- 🧭 **导航员** — 与 coder 结对编程，轮换驾驶员/导航员角色，边写边审

**协作模式：**
- coder ⟷ codereviewer 直接交互（结对编程，上限 3 轮）→超出升级 main
- 使用不同模型（coder=deepseek/deepseek-v4-pro, codereviewer=zai/glm-5.1），互补优势
- coder 遇到不确定的设计选择时，可实时咨询 codereviewer
- codereviewer 审查时发现问题 → 直接与 coder 讨论修复方案

**详细行为准则：** `codereviewer/AGENTS.md` | **人格定义：** `codereviewer/SOUL.md`

---

## 3. 通信矩阵

```
        ┌────────┬────────┬────────┬─────────┬───────────┬──────────────┐
        │  main  │ coder  │ tester │ auditor │ publicist │ codereviewer │
────────┼────────┼────────┼────────┼─────────┼───────────┼──────────────┤
 main   │   -    │  ↔️     │  ↔️     │  ↔️      │    ↔️      │     ↔️        │
 coder  │  ↔️     │   -    │  ↔️     │  ↔️      │    —      │     ↔️        │
 tester │  ↔️     │  ↔️     │   -    │  ↔️      │    —      │     —        │
auditor │  ↔️     │  ↔️     │  ↔️     │   -     │    —      │     —        │
publicist│ ↔️    │   —    │   —    │   —     │    -      │     —        │
codereviewer│ ↔️ │  ↔️    │   —    │   —     │    —      │     -        │
────────┴────────┴────────┴────────┴─────────┴───────────┴──────────────┘

publicist 只与 main 通信（写作需求唯一来源）
codereviewer 与 main、coder 通信（审查闭环），不与 tester/auditor 直接交互
其余 agent 全部互通
```

**关键交互规则：**
- main ↔ 所有 agent（调度中心）
- coder ↔ codereviewer（代码审查闭环，上限 2 轮）
- coder ↔ tester（bug 修复闭环，上限 3 轮）
- auditor 发现问题 → main 决策（不直接指挥 coder/tester）
- tester → main → auditor（审计通过 main 转手）
- codereviewer 发现设计问题 → 升级 main 裁决（不在审查报告中展开）

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
├── code-review-report.md   ← codereviewer 产出，代码审查报告（可选）
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
      model: "deepseek/deepseek-v4-pro"

    - id: "auditor"
      workspace: "~/.openclaw/workspace-auditor"
      model: "deepseek/deepseek-v4-pro"

    - id: "publicist"
      workspace: "~/.openclaw/workspace-publicist"
      model: "deepseek/deepseek-v4-pro"

    - id: "codereviewer"
      workspace: "~/.openclaw/workspace-codereviewer"
      model: "deepseek/deepseek-v4-pro"

tools:
  agentToAgent:
    enabled: true
    allow: ["main", "coder", "tester", "auditor", "publicist", "codereviewer"]

bindings:
  - agentId: "main"
    match:
      channel: "*"
      accountId: "*"
```

### 6.2 Projects 软链接

每个 agent workspace 必须有 `projects` 软链接指向 main workspace：

```bash
for agent in coder tester auditor publicist codereviewer; do
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

---

## 8. 文档变更流程

### 8.1 核心原则

**所有文档只能增量修改（追加/修订），不能覆盖整个文件从零开始。**

### 8.2 修改规则

| 文档类型 | 修改方式 | 示例 |
|---------|---------|------|
| AGENTS.md / SOUL.md | 追加新段落，补充说明 | 在协作须知下追加结对编程段落 |
| multi-agent-design.md | 修订受影响章节，版本号+1 | 修改§2.6 模型，v3.6→v3.7 |
| spec.md / design.md | 追加变更记录，原文保留 | 在文档底部追加需求变更记录 |
| todo.md / journey.md | 逐行追加，不删除已完成项 | 在文件末尾追加新条目 |
| README.md | 追加新功能段落 | 在功能列表后追加新功能 |

### 8.3 禁止行为

- ❌ `git rm` 后重新创建文件
- ❌ 用 `> file` 重定向覆盖
- ❌ 用完整文件内容替换（如 AI 生成整个新文件替代原文件）
- ❌ 不查看原文件内容就开写

### 8.4 正确流程

1. **先读取** — `read` 了解文件完整内容
2. **定位** — 找到需要修改的段落位置
3. **精确修改** — 用 `edit` 替换目标段落，或 `write` 追加到文件末尾
4. **验证** — 确认修改后的文件内容合理，没有丢失上下文
5. **提交** — `git add` + `git commit`，commit message 标明改动位置
