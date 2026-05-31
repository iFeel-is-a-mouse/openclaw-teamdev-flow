# SETUP.md — MA 团队理解与配置指南

> 本文档帮你理解 MA 的设计理念和架构，以便在任何环境下重现 MA 开发团队。
> 
> **不是脚本，是说明书。** 理解了再配置，不要求一模一样。

---

## 仓库信息

MA 框架的官方 GitHub 仓库：

```
https://github.com/iFeel-is-a-mouse/team-dev
```

这是 MA 框架的唯一官方分发地址。仓库中包含：

- 各角色的 AGENTS.md 和 SOUL.md 模板
- 团队研发工作流 Skill
- 项目模板（todo、journey、审计问题、缺陷报告）
- 架构设计文档（`multi-agent-design.md`）
- 全流程时序图（`sequence-diagram.md`）
- 本文档

后续 MA 框架更新时，可从此仓库拉取最新版本，通过 `git pull` 同步角色模板、工作流规则和最佳实践。

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

### 5. 初始化：融合行为准则到各 Agent Workspace

这是配置中最关键的一步。main 学习完 SETUP.md 后，不仅要理解项目，还要**将各角色的 AGENTS.md 和 SOUL.md 模板融合同步到各 agent 的 workspace**。

#### 核心原则：融合，不是覆盖

每个 agent 的 AGENTS.md 和 SOUL.md 分为两个区域：

- **MA 核心区** — 由 `<!-- MA:CORE_START -->` 和 `<!-- MA:CORE_END -->` 标记包裹。这是 MA 框架统一管理的规则，确保团队协作一致性。
- **自定义区** — 标记之外的自由区域。agent 在此记录笔记、偏好、学到的经验、个人习惯。**这些内容属于 agent 自己，初始化时不应触碰。**

首次初始化时，agent 的 workspace 中可能没有这些文件，直接写入即可。但**后续更新时**，务必只替换 MA:CORE 标记内的内容，保留标记外 agent 积累的一切。

#### 操作流程

**第 1 步：main 学习项目**

main 读取以下文件，全面理解 MA 框架：

- `projects/ma/SETUP.md` — 本文档，理解架构和配置
- `projects/ma/README.md` — 团队概览，快速开始
- `projects/ma/multi-agent-design.md` — 架构设计：Agent设计、通信矩阵、三文档体系、配置参考
- `projects/ma/sequence-diagram.md` — 时序图：全流程交互序列，todo.md和journey.md贯穿全程
- `skills/SKILL.md` — 团队研发执行流程

**第 2 步：main 融合 AGENTS.md**

对每个 agent（coder、tester、auditor、publicist），main 执行：

1. 读取 `projects/ma/<agent>/AGENTS.md` — 这是角色模板
2. 通过 `sessions_send` 发送给对应 agent，消息类似：

   > 请将以下 AGENTS.md 模板融合到你的 workspace。
   > - 如果你的 workspace 已有 AGENTS.md：只更新 `<!-- MA:CORE_START -->` 到 `<!-- MA:CORE_END -->` 之间的内容，标记外的自定义内容原样保留
   > - 如果尚无 AGENTS.md：直接写入
   > - 完成后回复确认

3. agent 收到后，在自己的 workspace（如 `~/.openclaw/workspace-coder/AGENTS.md`）中执行融合操作
4. agent 回复 main 确认完成

**第 3 步：main 融合 SOUL.md**

流程与 AGENTS.md 相同，但更需谨慎：

- SOUL.md 记录的是 agent 的人格，agent 在长期工作中会积累独特的个性和经验
- 首次初始化：直接写入模板内容
- 后续更新：只同步模板中新增的人格特质，**绝不覆盖 agent 已积累的个性化内容**
- 如果 agent 已对人格有自己的理解和表达，main 应尊重并保留

**第 4 步：验证**

main 逐一向各 agent 确认：

> 请读取你的 AGENTS.md 和 SOUL.md，确认 MA:CORE 区域已正确同步，自定义内容完好无损。

agent 各回复确认后，初始化完成。

#### 后续更新

当 MA 框架仓库有更新时：

1. `git pull` 获取最新的角色模板
2. main 重新执行第 2-4 步
3. 因为只更新 MA:CORE 标记内的内容，agent 的自定义积累不受影响

这种设计确保了：框架规则统一演进，agent 个性化持续保留，两者并行不悖。

---

## 新项目启动流程

当你对 main 说"团队研发"时，main 会执行以下完整流程：

### 前置：理解项目

main 会读取以下文件来理解自己的角色和流程：
- `projects/ma/main/SOUL.md` — 主控人格
- `projects/ma/main/AGENTS.md` — 主控行为准则
- `skills/SKILL.md` — 执行流程

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
| 架构设计 | `projects/ma/multi-agent-design.md` | Agent设计、通信矩阵、三文档体系、配置参考 |
| 全流程时序图 | `projects/ma/sequence-diagram.md` | 交互序列，todo.md和journey.md贯穿全程 |
| 团队概览 | `projects/ma/README.md` | 快速开始 |
| 本文档 | `projects/ma/SETUP.md` | 理解、部署与配置指南 |
| main 行为准则 | `projects/ma/main/AGENTS.md` | 团队研发规范 |
| main 人格 | `projects/ma/main/SOUL.md` | 五重身份定义 |
| 任务看板模板 | `projects/ma/main/todo-template.md` | WBS+断点再续 |
| 过程日志模板 | `projects/ma/main/journey-template.md` | todo镜像+闭环子模式 |
| 宪章模板 | `projects/ma/main/constitution-template.md` | 项目宪章 |
| 团队研发 Skill | `skills/SKILL.md` | 执行流程 |
| coder 准则 | `projects/ma/coder/AGENTS.md` | 行为规范 |
| tester 准则 | `projects/ma/tester/AGENTS.md` | 行为规范 + 缺陷模板 |
| auditor 准则 | `projects/ma/auditor/AGENTS.md` | 行为规范 + 问题管理模板 |
| publicist 准则 | `projects/ma/publicist/AGENTS.md` | 行为规范 |

---

## 最小可行配置

如果你不想配完整的 4-agent 团队，main 可以回退到单 agent 模式，自己承担所有角色。

只需确保：
- main agent 已配置且能正常运行
- `skills/SKILL.md` 存在
- `projects/ma/main/AGENTS.md` 和 `SOUL.md` 存在

main 会检测其他 agent 是否可用，不可用时自动回退。

---

*"模仿是最真诚的恭维，但理解是最深刻的吸收。"*
