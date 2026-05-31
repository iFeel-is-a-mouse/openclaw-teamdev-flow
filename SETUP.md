# SETUP.md — MA 团队理解与配置指南

> 本文档帮你理解 MA 的设计理念和架构，以便在任何环境下重现 MA 开发团队。
> 
> **不是脚本，是说明书。** 理解了再配置，不要求一模一样。

---

## 理念：为什么这样设计

### 人类团队有什么，MA 就有什么

一支靠谱的软件开发团队至少需要这些角色：

| 角色 | 做什么 | 为什么不能缺 |
|------|--------|------------|
| **技术负责人** | 需求分析、方案决策、质量把关 | 没有把关人，质量靠运气 |
| **开发者** | 设计架构、写代码 | 这是产出层 |
| **测试** | 验证功能、发现 bug | 开发者测试自己的代码有盲区 |
| **审计/QA** | 独立审查、安全检查 | 开发和测试都可能遗漏，需要第三方视角 |
| **写文档的** | README、技术文档、总结 | 代码能跑 ≠ 可交付 |

MA 把这五个角色映射为五个 AI agent，每个 agent 专注一件事。

### 核心设计原则

1. **关注点分离。** 每个 agent 只做自己擅长的事。coder 不测试，tester 不审计，auditor 不写代码。
2. **独立视角。** auditor 不参与开发，保持第三方客观性。这比"同一个 agent 换种模式"有效得多。
3. **人机接口单一。** 用户只和 main 对话。main 负责调度，用户不需要知道背后有几个 agent 在工作。
4. **文件系统是通信总线。** 所有 agent 共享 `projects/` 目录。代码、文档、审计报告都在文件系统里，不需要消息队列或 API。
5. **信任但验证。** main 相信 agent 的能力，但每次关键产出都要审查。

---

## 架构总览

```
用户
  │
  ▼
main (主控) ─── 需求入口、结果出口、团队调度
  │
  ├── auditor (前置审计 + 终审)  ← 独立第三方
  ├── coder (设计 + 编码)
  ├── tester (测试)
  └── publicist (文档)
```

**通信方式：** `sessions_send`（agent 间发消息）+ 共享文件系统（`projects/` 目录）+ Git（代码版本控制）

**工作流（增强版，吸收 spec-kit）：**
```
0 constitution → 1 spec → 2 pre-audit+clarify → 3 gate → 4 design
→ 5 analyze → 6 implement → 7 test → 8 final-audit+checklist
→ 9 merge → 10 docs → 11 review
```

---

## 配置指南

### 1. Agent 创建

每个 agent 需要独立的 workspace，这样各自的 AGENTS.md 和 SOUL.md 不会互相干扰。

```bash
openclaw agents create coder    --workspace ~/.openclaw/workspace-coder
openclaw agents create tester   --workspace ~/.openclaw/workspace-tester
openclaw agents create auditor  --workspace ~/.openclaw/workspace-auditor
openclaw agents create publicist --workspace ~/.openclaw/workspace-publicist
```

main agent 就是你的默认 agent（workspace 为 `~/.openclaw/workspace`），无需额外创建。

### 2. 模型分配

| Agent | 推荐模型 | 原因 |
|-------|---------|------|
| main | deepseek-v4-pro | 需要深度推理做需求分析和决策 |
| coder | deepseek-v4-pro | 架构设计和编码需要深度思考 |
| tester | deepseek-v4-flash | 测试是验证性工作，flash 够快够用 |
| auditor | deepseek-v4-flash | 审计是检查性工作，flash 即可 |
| publicist | deepseek-v4-pro | 写作需要理解深度 |

> 这不是硬性规定。如果你用的模型体系不同，把握原则：**思考型工作用强模型，验证型工作用快模型。**

### 3. 关键配置：Projects 共享

**这是最容易出问题的环节。** agent 的 workspace 是独立的，但 `projects/` 目录必须共享。

核心原理：所有 agent 看到的 `projects/` 必须是同一个目录（main workspace 下的 `projects/`）。这样 agent A 写的文件，agent B 能直接读到。

实现方式——软链接：

```bash
for agent in coder tester auditor publicist; do
  ln -s ~/.openclaw/workspace/projects ~/.openclaw/workspace-$agent/projects
done
```

**为什么用软链接而不是别的方案？**
- 复制：agent 间文件不同步，A 改了 B 看不到
- Git 各自 clone：每个 agent 独立仓库，冲突和同步是噩梦
- 共享文件系统：最简方案，零配置，零延迟

### 4. Agent 间通信

在 OpenClaw 配置中启用 agent-to-agent 通信：

```yaml
agents:
  tools:
    agentToAgent:
      enabled: true
      allow: [main, coder, tester, auditor, publicist]
```

通信矩阵：
- main ↔ 所有 agent（main 是调度中心）
- coder ↔ tester（bug 修复闭环）
- auditor ↔ main（审计结果汇报，不直接指挥 coder/tester）
- publicist ↔ main（写作任务接收和交付）

### 5. Agent 行为准则部署

每个 agent 的 AGENTS.md 和 SOUL.md 定义其行为规范。模板见 `projects/ma/<agent>/`。

**部署方式：理解后自行配置，不是 cp。**

- **AGENTS.md** — 包含 `<!-- MA:CORE_START -->` ... `<!-- MA:CORE_END -->` 标记。标记内是 MA 统一管理的核心规则，标记外是 agent 的自定义内容（笔记、偏好、学到的经验）。
- **SOUL.md** — agent 的核心人格定义。建议保持一致，但如果你想让 agent 有不同的人格倾向，可以修改。

**首次配置：** 阅读 `projects/ma/<agent>/` 下的模板，理解每个 agent 的职责和规则，然后写入对应 agent 的 workspace。
**后续更新：** 修改模板后，用标记定位 MA:CORE 区块，只更新核心规则，保留 agent 的自定义内容。

---

## 新项目启动流程

当你对 main 说"团队研发"时，main 会执行以下完整流程：

### 前置：理解项目

main 会读取以下文件来理解自己的角色和流程：
- `projects/ma/main/SOUL.md` — 主控人格
- `projects/ma/main/AGENTS.md` — 主控行为准则
- `skills/team-dev/SKILL.md` — 执行流程

### 项目启动

1. main 基于 `projects/ma/main/constitution-template.md` 制定项目宪章
2. 创建项目目录结构（`src/`、`tests/`、`docs/` 等）
3. Git init

### 开发流程（吸收 spec-kit 精髓）

```
0 constitution — 项目宪章，不可妥协的原则
1 spec — 需求说明 + 代码风格
2 pre-audit + clarify — 审计需求完整性 + 识别歧义
3 main gate — 审查审计质量，决定是否下发
4 design — coder 设计架构方案
5 analyze — 跨文档一致性检查（spec ↔ design ↔ constitution）
6 implement — coder 编码
7 test — tester 黑盒+白盒测试
8 final-audit + checklist — 终审 + 生成质量清单逐项验证
9 merge — main 合并代码
10 docs — publicist 撰写 README + 项目总结
11 review — main 最终审阅 → 通知 用户
```

每个阶段产出明确的文档（constitution.md → spec.md → audit-report.md → design.md → checklist.md → README.md），形成完整的项目档案。

---

## 文件索引

| 文件 | 位置 | 用途 |
|------|------|------|
| MA 架构设计 | `projects/ma/multi-agent-design.md` | 完整设计文档 |
| 团队概览 | `projects/ma/README.md` | 快速了解 |
| 本文档 | `projects/ma/SETUP.md` | 理解与配置指南 |
| main 行为准则 | `projects/ma/main/AGENTS.md` | main 团队研发规范 |
| main 人格 | `projects/ma/main/SOUL.md` | main 五重身份定义 |
| 宪章模板 | `projects/ma/main/constitution-template.md` | 项目宪章模板 |
| 团队研发 Skill | `skills/team-dev/SKILL.md` | 执行流程 |
| coder 准则 | `projects/ma/coder/AGENTS.md` | coder 行为规范 |
| tester 准则 | `projects/ma/tester/AGENTS.md` | tester 行为规范 |
| auditor 准则 | `projects/ma/auditor/AGENTS.md` | auditor 行为规范 |
| publicist 准则 | `projects/ma/publicist/AGENTS.md` | publicist 行为规范 |

---

## 最小可行配置

如果你不想配完整的 4-agent 团队，main 可以回退到单 agent 模式，自己承担所有角色。

只需确保：
- main agent 已配置且能正常运行
- `skills/team-dev/SKILL.md` 存在
- `projects/ma/main/AGENTS.md` 和 `SOUL.md` 存在

main 会检测其他 agent 是否可用，不可用时自动回退。

---

*"模仿是最真诚的恭维，但理解是最深刻的吸收。"*
