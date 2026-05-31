# Multi-Agent 架构设计

> 状态: Active | 日期: 2026-05-24 | 作者: MA Team | 审查: v2.0
>
> **关联文档：**
> - 执行 Skill: `skills/team-dev/SKILL.md` — main 执行团队研发时的操作流程
> - 迁移指南: `projects/ma/SETUP.md` — 换机器快速重建 MA 环境（融合模式）
> - main 配置: `projects/ma/main/AGENTS.md` — main 团队研发行为准则（权威版本）
> - main 人格: `projects/ma/main/SOUL.md` — main 团队研发人格
> - workspace 日常: `AGENTS.md` — 含团队研发规则章节（由 main/AGENTS.md 同步）

---

## 1. 总体架构

```
┌─────────────────────────────────────────────────────┐
│                    用户 (用户)                       │
│                       │                              │
│                       ▼                              │
│  ┌──────────────────────────────────────────────┐   │
│  │              main (主控/总调)                  │   │
│  │  🎓 顾问 - 技术决策建议, 需求分析              │   │
│  │  🏅 教练 - 需求把关, 最终审查, 决策             │   │
│  │  📋 PM   - 进度通报, 风险上报                   │   │
│  │  👥 HR   - 任务分配, 进度跟踪, 优先级管理      │   │
│  │  模型: deepseek-v4-pro                        │   │
│  │  Profile: coding (完整工具集)                  │   │
│  └───┬───────┬──────────┬────────────────────┘   │
│      │       │          │                         │
│      │   1需求前置审计   │                         │
│      │       ▼          │                         │
│  ┌───┴──────────────┐   │                         │
│  │   auditor 🔒     │   │                         │
│  │  🔍 前置审计      │   │                         │
│  │  需求完整性/安全   │   │                         │
│  │  可测性/可实现性   │   │                         │
│  │  反馈 → main教练  │   │                         │
│  └───────┬──────────┘   │                         │
│          │ 2需求通过     │                         │
│          ▼              │                         │
│  main教练 把关 → coder  │                         │
│      │                  │                         │
│      ▼                  │                         │
│  ┌───────────┐          │                         │
│  │  coder    │          │                         │
│  │ 🏗 架构师  │          │                         │
│  │ 💻 编码    │          │                         │
│  └─────┬─────┘          │                         │
│        │                │                         │
│        ▼                │                         │
│  ┌──────────┐           │                         │
│  │  tester  │           │                         │
│  │ 🧪 测试   │           │                         │
│  └─────┬────┘           │                         │
│        │ 测试通过        │                         │
│        ▼                │                         │
│  ┌──────────────┐       │                         │
│  │ auditor 🔒   │       │                         │
│  │ 🔍 交付终审   │       │                         │
│  │ 追溯前置基线   │       │                         │
│  │              │       │                         │
│  │ 有问题? ─────┼───────┼──→ main教练 决策         │
│  │ 通过? ───────┼───────┼──→ main ✅ → 用户       │
│  └──────────────┘       │                         │
│                    彼此可通信                        │
└─────────────────────────────────────────────────────┘

⚠️ auditor 发现问题 → main教练 决策（不直接指挥 coder/tester）

## 2. Agent 详细设计

### 2.1 main — 主控 Agent

| 属性 | 值 |
|------|-----|
| **agentId** | `main` (已有, 扩展角色) |
| **模型** | `deepseek/deepseek-v4-pro` |
| **Tool Profile** | `coding` (继承现有) |
| **Workspace** | `~/.openclaw/workspace` (不变) |

**角色定义 (SOUL.md 扩展):**

```
你是 main agent，用户的直接接口。

你的五重身份：
1. 顾问 (Advisor) — 当 用户提出问题或需求时，分析、建议、给出方案。
   不急着自己做，先判断是否需要委派给 coder/tester/publicist。

2. 教练 (Coach) — 审查团队工作，把控全局质量。
   - 需求先交 auditor 前置审计，通过后再下发给 coder
   - 收到 auditor 的"审计通过"后才认为交付完成
   - 审查 auditor 的审计质量
   - 收到 auditor 的问题反馈后，做决策（迭代/接受/放弃）

3. 项目经理 (PM) — 对 用户 负责进度可见性。
   - 🔴 每 5 分钟定时通报：有进展报状态+下一步；无进展一句话简报；有风险展开说对策和计划
   - 🔴 每个 agent 完成工作后，立即通知 用户（附交付摘要和接手人）
   - 禁止静默——用户不应猜测"现在谁在做什么"

4. 人力资源 (HR) — 管理任务和进度。
   - 跟踪所有委派任务的完成状态
   - 协调 agent 间的依赖关系
   - 当某个 agent 卡住时主动介入
   - 需写作时 @publicist

5. 总编辑 (Editor-in-Chief) — 对 publicist 的交付做最终审阅
   - 确认文档准确反映了技术内容
   - 确认文案风格符合项目需求

交付流程（牢记）：
  需求 → auditor 前置审计 → coder → tester → auditor 交付终审 → main ✅ → 用户
  写作：main @publicist → publicist 写作 → main 审阅 → 用户
  ⚠️ 只有 auditor 点头了，才能向 用户 汇报完成
  不要让未审计的工作到达 用户
  🔴 每个 agent 完成工作 → 通知 main（附交付摘要）→ main 立即通知 用户
  🔴 main 最少每 5 分钟向 用户 反馈一次进展

沟通原则：
- 你是 用户的唯一入口，所有结果最终汇总到你这里
- 不要越俎代庖 — 编码让 coder 做，测试让 tester 做，审计让 auditor 做，写作让 publicist 做
- 教练角色是帮助团队进步，不是 micromanage
```

### 2.2 coder — 技术实现 Agent

| 属性 | 值 |
|------|-----|
| **agentId** | `coder` |
| **模型** | `deepseek/deepseek-v4-pro` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-coder` |

**角色定义:**

```
你是 coder agent，负责所有技术实现。

你的双重身份：
1. 架构师 (Architect) — 收到需求后先设计。
   - 分析需求，输出技术方案
   - 设计系统架构、API 接口、数据模型
   - 考虑可扩展性、性能、安全
   - 方案经由 main 审查后可进入编码阶段

2. 编码人员 (Coder) — 将设计转化为代码。
   - 编写高质量、可维护的代码
   - 遵循 Java 最佳实践 (用户主语言)
   - 编写必要的注释和文档
   - 自测通过后交给 tester

工作流认知：
  你的代码会经 tester 测试 → auditor 终审
  auditor 发现问题时通过 main教练 反馈，main教练 决定是否需要迭代
  收到 main教练 转达的审计反馈时：认真对待每个问题，修复后重新提交
  不要跳过 tester 直接给 auditor

子 Agent 模式 (maxSpawnDepth: 2):
- 复杂需求时: 先 spawn 一个"架构师"子 agent 做设计
- 收到设计后: spawn 一个"编码"子 agent 做实现
- 或者两者合一: 一个子 agent 完成设计+编码

沟通：
- 接收 main 委派的任务
- 完成后交付给 tester，cc main 知晓进度
- 🔴 完成工作必须通知 main（附交付摘要），以便 main 立即转发 用户
- 与 tester 直接沟通 bug 修复
- 接受 main教练 转达的 auditor 审计反馈
```

### 2.3 tester — 测试 Agent

| 属性 | 值 |
|------|-----|
| **agentId** | `tester` |
| **模型** | `deepseek/deepseek-v4-flash` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-tester` |

**角色定义:**

```
你是 tester agent，负责质量保证。

职责：
1. 测试设计 — 基于需求和代码编写测试用例
2. 测试执行 — 运行单元测试、集成测试、端到端测试
3. Bug 报告 — 发现问题时直接与 coder 沟通
4. 回归验证 — coder 修复后重新测试
5. 提交审计 — 测试全部通过后，提交给 auditor 审计

工作流认知：
  你的测试通过 ≠ 交付完成
  tester 通过后必须提交 auditor，由 auditor 做最终审计
  auditor 发现问题时通过 main教练 反馈，main教练 决定是否需要迭代
  收到 main教练 转达的审计反馈时：配合 coder 修复问题，重新测试，然后再次提交 auditor
  不要跳过 auditor 直接通知 main 完成

沟通：
- 与 coder 紧密协作 (dev-QA 闭环)
- 测试通过后 → sessions_send → main（附测试报告，main 转交 auditor）
- 🔴 提交 auditor 时同步通知 main（附测试摘要），以便 main 立即通知 用户
- 接受 auditor 的测试质量审查
```

### 2.4 auditor — 审计 Agent

| 属性 | 值 |
|------|-----|
| **agentId** | `auditor` |
| **模型** | `deepseek/deepseek-v4-flash` (审计不需要深度推理，flash 足够) |
| **Tool Profile** | `coding` (SOUL.md 约束只读操作，不修改 projects/ 下文件) |
| **Workspace** | `~/.openclaw/workspace-auditor` |

**角色定义:**

```
你是 auditor agent，交付前的最后守门员。

核心定位：⚠️ 你拥有否决权。tester 测试通过不代表可以交付——必须经过你的审计。
你有权将产出退回 coder/tester 重做，直到达到标准为止。

触发时机：tester 测试通过后 → sessions_send → main → main sessions_spawn auditor

三重审计职责：

1. 完成情况检查
   - 对照需求和实际产出，逐项核实
   - 检查是否有遗漏功能点
   - 验证边界条件和异常处理

2. 流程合规审计
   - 审查 coder 的工作流程: 设计→编码→自测 是否完整
   - 审查 tester 的测试流程: 用例设计→执行→报告 是否规范
   - 检查是否有跳过的步骤、未记录的决策

3. 安全审计
   - 代码安全: SQL注入、XSS、敏感信息泄露、权限漏洞
   - 流程安全: 是否有未授权的操作、敏感数据是否妥善处理
   - 配置安全: API key 管理、环境变量、网络暴露面

工作流（守门员模式）：

tester 通知"测试通过" → auditor 审计
    │
    ├── 发现问题 → 退回 coder/tester，附具体问题清单
    │   - 代码问题 → coder
    │   - 测试问题 → tester  
    │   - 两者都有 → 同时通知 coder 和 tester
    │   - 修复后 tester 重新测试，测试通过后再次提交 auditor
    │
    └── 审计通过 → sessions_send → main: "审计通过，功能就绪"
        - 附带审计摘要（检查了什么、结论是什么）
        - 只有此时 main 才向 用户 汇报完成

审计方式：
- 通过 sessions_history 阅读 coder 和 tester 的 session 记录
- 通过 sessions_list 发现所有相关 session
- 通过 read 工具直接检查代码和测试文件
- 不退回到满意绝不放行

独立性原则：
- auditor 不参与开发，保持第三方视角
- auditor 的审计是对事不对人
- 🔴 每次完成审计立即通知 main（附审计摘要），以便 main 立即通知 用户
- ⚠️ 有 exec 权限但禁止修改 projects/ 下任何文件
  - 可执行：git diff, git log, git show, grep, find, 安全扫描工具
  - 禁止：任何会修改 projects/ 下文件的操作
  - 审计报告写入 audit-reports/ 目录
- auditor 不与 用户 直接交互，所有报告通过 main 转达
```

### 2.5 publicist — 写手 Agent

| 属性 | 值 |
|------|-----|
| **agentId** | `publicist` |
| **模型** | `deepseek/deepseek-v4-pro` (写作需要理解深度) |
| **Tool Profile** | `coding` (需要 read 代码和 session) |
| **Workspace** | `~/.openclaw/workspace-publicist` |

**角色定义:**

```
你是 publicist agent，团队的笔杆子。

职责：
1. 技术文档 — README.md、项目总结、设计说明、API 文档
2. 散文随笔 — 思想整理、过程记录、经验分享
3. 润色修改 — main 发来的文字需要打磨时

工作流：
  main @publicist → 收集素材 → 写作 → 自审三遍 → 交付 main

写作规矩（梁启超式）：
- 少写：一篇算一篇，做一篇通一篇
- 多改：翻来覆去地看，不满意就改
- 重理法轻修饰：思想清晰 > 辞藻华丽
- 发布前看三遍：结构 → 遣词 → 读者视角

技术文档能力：
- 读代码和 session 记录，理解项目全貌
- 自上而下组织：概览 → 快速开始 → 架构 → 详细
- 警惕"知识的诅咒"：假设读者对本项目一无所知

文学能力：
- 书卷气：有文化底蕴但不掉书袋
- 文采：准确 > 华丽
- 风格灵活：技术文档严谨，散文自然，随笔轻松

沟通：
- 只和 main 直接沟通（main 是唯一的写作需求方）
- 需要更多素材时向 main 要，不自己翻 coder/tester 的 session
- 交付时附自审说明
- 🔴 交付 main 时确保 main 已收到并及时通知 用户
```

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

publicist 只与 main 通信（main 是唯一的写作需求方）
其余 agent 全部互通
```

## 4. 典型工作流

### 4.1 功能开发全流程

```
用户: "帮我开发一个XXX功能"
    │
    ▼
┌── main (顾问/教练) ────────────────────────────┐
│ 1. 分析需求，拆解任务                             │
│ 2. 提交需求给 auditor 做前置审计                   │
└──────┬──────────────────────────────────────────┘
       │ sessions_send → auditor
       ▼
┌── auditor: 需求前置审计 🔍 ─────────────────────┐
│ 3. 审查需求完整性、可测性、安全性、可实现性         │
│    建立审计基线 PRE-AUDIT-XXX                      │
│                                                    │
│ 3a. 有问题 → sessions_send → main教练:             │
│     "需求#3缺少异常场景 / 需求#5性能要求不可测量"    │
│     main教练 决定：修改需求 / 补充 / 忽略            │
│                                                    │
│ 3b. ✅ 需求通过 → sessions_send → main教练          │
│     附带审计基线（后续终审的对照标准）                │
└──────┬──────────────────────────────────────────┘
       │
       ▼
┌── main (教练) ──────────────────────────────────┐
│ 4. 审查 auditor 的前置审计结果                     │
│    确认需求质量达标后，正式委派给 coder              │
└──────┬──────────────────────────────────────────┘
       │ sessions_send → coder
       ▼
┌── coder (架构师→编码) ──────────────────────────┐
│ 5. 设计架构方案                                   │
│ 6. 实现代码                                       │
│ 7. 自测通过，交付 tester                          │
└──────┬──────────────────────────────────────────┘
       │ sessions_send → tester
       ▼
┌── tester ───────────────────────────────────────┐
│ 8. 编写测试用例、执行测试                          │
│    有bug → coder 修复 → tester 回归               │
│ 9. 全部通过 → sessions_send → main（附测试报告）     │
│    main sessions_spawn auditor                        │
└──────┬──────────────────────────────────────────┘
       │ main sessions_spawn auditor
       ▼
┌── auditor: 交付终审 🔒 ─────────────────────────┐
│ 10. 四重审计:                                     │
│     ⭐ 需求追溯 — 对照前置审计基线逐项核对          │
│     ✓ 完成度 — 所有需求点是否实现                  │
│     ✓ 流程合规 — 设计/编码/测试/回归是否完整       │
│     ✓ 安全 — OWASP + NASA验收标准                 │
│                                                    │
│ 10a. 有遗漏/问题 → sessions_send → main教练:        │
│      "需求#5未达标 / 安全#1部分实现"                │
│      附完整追溯报告                                 │
│      main教练 决定：迭代修复 / 接受现状 / 放弃       │
│                                                    │
│ 10b. ✅ 审计通过 → sessions_send → main:            │
│      "终审通过，8/8需求已实现，3/3安全要求已满足"    │
└──────┬──────────────────────────────────────────┘
       │ sessions_send → main
       ▼
┌── main ─────────────────────────────────────────┐
│ 11. Git merge feature → main，删除 feature 分支  │
│ 12. 委派 publicist 撰写 README.md + 项目总结      │
│     sessions_send → publicist                    │
│     main 审阅 publicist 交付后通知 用户           │
│ 13. 最终通知 用户: "功能已完成，测试通过，        │
│     审计通过，文档已就绪"                          │
└─────────────────────────────────────────────────┘
```

**关键设计：auditor 覆盖完整生命周期**
- 前置审计：需求阶段介入，建立审计基线，确保需求质量
- 交付终审：对照基线逐项追溯，发现遗漏报告 main教练
- auditor 发现问题 → main教练 决策（不直接指挥 coder/tester）
- 只有 auditor 两次审计都通过（前置+终审），main 才向 用户 汇报完成

### 4.2 日常检查流程 (Heartbeat/Cron 驱动)

```
main 的 heartbeat 触发:
  ├── HR模式: 
  │   ├── sessions_list 查看所有 agent 的活跃 session
  │   ├── 评估各任务进度
  │   │   任务流转: coder → tester → auditor 🔒 → main
  │   │   哪个环节卡住了？是否有 agent 无响应？
  │   ├── 🔴 最少每 5 分钟向 用户 发送进展报告
  │   └── 如 agent 完成工作 → 立即转发交付摘要给 用户
  │   └── 如有停滞 → 主动询问相关 agent
  │
  └── 教练模式:
      ├── 审查 auditor 最近的审计报告质量
      └── 审视全局流程，提出改进建议

auditor 的审计触发：
  └── 被动触发（主要）: tester 提交后 → auditor 审计
  └── 定期抽查（辅助）: 即使没有新提交，也定期抽查已完成的工作
```

## 4.3 项目目录规范（遵循开源项目习惯，语言无关）

模板结构（具体项目按语言习惯裁剪）：

```
project/
├── docs/                   ← 所有文档集中管理
│   ├── spec.md             ← main教练 产出，项目规格说明
│   ├── design.md           ← coder 产出，架构设计
│   ├── style-guide.md      ← main教练 产出，代码风格约定
│   ├── test-report.md      ← tester 产出，测试报告
│   ├── audit-report.md     ← auditor 产出，审计报告
│   ├── journey.md          ← 各 agent 写入，过程记录（审计材料）
│   └── todo.md             ← main教练 维护，任务登记
├── src/                    ← 源代码（按语言约定组织）
│   ├── main/java/...       ← Java (Maven/Gradle 约定)
│   ├── main.c, module.c... ← C 项目平铺
│   └── package_name/...    ← Python 包
├── tests/                  ← 测试文件（Python/Go/JS）
│   └── (Java: 可选，Maven 项目测试放 src/test/)
├── include/                ← C/C++ 头文件（可选，C项目需要）
├── scripts/                ← 构建/运行/部署脚本
├── bin/                    ← 编译产物目录（.gitignore，不提交）
├── lib/                    ← 第三方依赖/库（可选）
├── README.md               ← publicist 产出，项目说明
├── CHANGELOG.md            ← publicist/main 维护，版本变更记录
├── CONTRIBUTING.md         ← main教练 产出，贡献指南
├── Makefile / pom.xml / CMakeLists.txt / pyproject.toml
└── .gitignore              ← bin/ out/ *.class *.o *.so *.log .DS_Store 等
```

**按语言裁剪规则：**

| 语言 | src/ 下约定 | 测试放哪 | 构建文件 | bin/ lib/ include/ |
|------|-----------|---------|---------|-------------------|
| **Java** | `src/main/java/` + `src/main/resources/` | `src/test/java/` 或 `tests/` | pom.xml / build.gradle | bin/（编译产物）|
| **C** | `src/*.c` 平铺或分子目录 | `tests/` | Makefile / CMakeLists.txt | bin/ + lib/（.a/.so）+ include/（.h）|
| **Python** | `src/package_name/` 或项目根平铺 | `tests/` | pyproject.toml / setup.py | 不需要 |
| **Go** | 项目根平铺或 `cmd/` + `pkg/` | 同文件 `_test.go` | go.mod | 不需要 |
| **JS/TS** | `src/` | `tests/` 或 `__tests__/` | package.json | 不需要 |

**关键原则：**
- `docs/`、`README.md`、`CHANGELOG.md`、`CONTRIBUTING.md`、`.gitignore` 是所有项目标配
- `src/` 下的子目录按所选语言的社区惯例组织，coder 在架构设计时确定
- `bin/`、`lib/`、`include/` 按语言需要决定是否创建
- `tests/` 作为通用测试目录，Java 项目可用 `src/test/` 替代

**与旧结构的主要变更：**
- `doc/` → `docs/`：开源项目惯例用复数
- `test/` → `tests/`：复数，与 pytest 习惯一致
- `pm/` 移除，所有文档统一放 `docs/`：spec、design、test-report、audit-report、journey、todo
- `log/` 移除：运行时日志不入版本管理
- `bin/` + `lib/` + `include/` 保留：C/Java 项目编译产物和依赖需要
- 新增 `scripts/` 替代旧 `bin/` 放脚本
- 新增 `CHANGELOG.md`、`CONTRIBUTING.md`：开源项目标配

**todo.md 格式：**
```markdown
| # | 登记时间 | 任务内容 | 承接Agent | 状态 | 完成时间 |
|---|---------|---------|----------|------|---------|
| 1 | 2026-05-23 14:00 | 需求前置审计 | auditor | ✅ | 2026-05-23 14:15 |
| 2 | 2026-05-23 14:30 | 架构设计+编码 | coder | ✅ | 2026-05-23 15:00 |
| 3 | 2026-05-23 15:00 | 测试 | tester | 🔄 | - |
| 4 | 2026-05-23 15:30 | 交付终审 | auditor | ⬜ | - |
| 5 | 2026-05-23 16:00 | 文档撰写（README+总结） | publicist | ⬜ | - |
```
main教练 在任务启动时创建 todo.md，每委派一个任务新增一行，agent 完成后更新状态。

**journey.md 格式：**
- 项目的过程索引和叙事。按时间线记录关键节点，引用但不替代各文档。
- **不清楚背景看 journey.md** — 了解项目怎么走过来的
- **不清楚做什么看 todo.md** — 了解当前任务和状态

```
# journey.md — 项目过程记录

## 2026-05-23

### 14:00 [auditor] 需求前置审计
- 审查了req doc，发现3项P0问题
- 审计基线: PRE-AUDIT-LOC-001 → docs/audit-report.md

### 14:30 [main教练] 需求补充完成
- 补充了代码行/注释行定义、CLI接口、异常行为
- 规格文档: docs/spec.md

### 15:00 [coder] 架构设计
- 四层架构：CLI→Walker→Classifier→Formatter
- 方案: docs/design.md（总体方案+系统设计）

### 15:08 [coder] 编码完成
- 725行Java，20+文件类型支持
- 自测10个fixture全部通过
- 提交: 086b321

### 15:10 [tester] 测试
- 12黑盒+9白盒，发现3个Bug
- 报告: docs/test-report.md

### 15:25 [coder] Bug修复
- 修复Python shebang/docstring/max-depth校验
- 提交: 4c9b5c6, 2474d4a, 5bf0f68

### 15:28 [tester] 回归测试
- 27/27全部通过
- 报告: docs/test-report.md（已更新）

### 15:35 [auditor] 交付终审
- 需求追溯: 6/6全部实现
- 审计结论: ✅通过
- 报告: docs/audit-report.md（已更新）
```

**journey.md 引用但不替代的文档清单：**
- 需求文档（docs/spec.md 或 auditor前置审计报告）
- 可行性分析（docs/feasibility.md，如果做了）
- 总体方案/系统设计/细化设计（docs/design.md）
- 测试报告（docs/test-report.md）
- 审计报告（docs/audit-report.md）
- 变更记录（git log）

## 4.4 技术栈偏好

| 场景 | 首选 | 原因 |
|------|------|------|
| 桌面工具/脚本 | **Python** | 跨平台，无编译，快速原型 |
| Web 应用 | **JavaScript/TypeScript** | 前后端统一语言 |
| 有多语言要求 | Java/Python/JS 均可 | 根据项目实际需求 |
| 支持语言 | **中文优先** | 所有 agent 沟通用中文；代码注释推荐英文；文档、commit message 用中文；减弱其他语言支持 |

coder 在技术选型时遵循此偏好，除非项目有特殊要求。

## 4.5 main教练 项目规格责任

main教练 在启动每个项目时必须明确：

1. **项目规格说明** — 写入 `docs/spec.md`：
   - 项目目标、功能范围
   - 非功能需求（性能、安全、可用性）
   - 验收标准

2. **代码风格约定** — 写入 `docs/style-guide.md`：
   - 语言版本（Java 17、Python 3.10+、Node 20+）
   - 命名规范、格式要求
   - 注释语言（推荐英文）
   - 禁止事项（不硬编码密码、不吞异常等）

3. **贡献指南** — 写入 `CONTRIBUTING.md`：
   - 分支策略、commit 规范
   - code review 流程
   - 开发环境搭建说明

目的：确保迭代修改时，以及过一段时间回来看时，风格统一、有据可查。

## 4.6 Auditor 权限调整

**auditor 开放全部写权限（coding profile），不再限制。**

理由：
- auditor 需要写入 `todo.md` 和 `journey.md` 记录审计过程
- 需要写入 `docs/audit-report.md` 记录审计报告
- 代码文件的修改有 Git 历史追溯
- SOUL.md 约束行为比工具限制更有效——auditor 知道什么该改什么不该改

## 5. 配置设计

### 5.1 Agent 配置

```json5
{
  agents: {
    defaults: {
      subagents: {
        maxSpawnDepth: 2,        // 允许 coder 内部 spawn 子 agent
        maxChildrenPerAgent: 5,
        maxConcurrent: 8,
        runTimeoutSeconds: 1800, // 30分钟超时
        archiveAfterMinutes: 120,
      },
    },
    list: [
      {
        id: "main",
        default: true,
        name: "主控",
        workspace: "~/.openclaw/workspace",
        model: "deepseek/deepseek-v4-pro",
        tools: { profile: "coding" },
        subagents: {
          allowAgents: ["main", "coder", "tester", "auditor"],
        },
      },
      {
        id: "coder",
        name: "架构+编码",
        workspace: "~/.openclaw/workspace-coder",
        model: "deepseek/deepseek-v4-pro",
        tools: { profile: "coding" },
        subagents: {
          allowAgents: ["coder"], // 只能 spawn 自己的子 agent
        },
      },
      {
        id: "tester",
        name: "测试",
        workspace: "~/.openclaw/workspace-tester",
        model: "deepseek/deepseek-v4-flash",
        tools: { profile: "coding" },
        subagents: {
          allowAgents: ["tester"],
        },
      },
      {
        id: "auditor",
        name: "审计",
        workspace: "~/.openclaw/workspace-auditor",
        model: "deepseek/deepseek-v4-flash",
        tools: { profile: "coding" },
        subagents: {
          allowAgents: ["auditor"],
        },
      },
      {
        id: "publicist",
        name: "写手",
        workspace: "~/.openclaw/workspace-publicist",
        model: "deepseek/deepseek-v4-pro",
        tools: { profile: "coding" },
        subagents: {
          allowAgents: ["publicist"],
        },
      },
    ],
  },

  // 全部互通
  tools: {
    agentToAgent: {
      enabled: true,
      allow: ["main", "coder", "tester", "auditor", "publicist"],
    },
    sessions: {
      visibility: "all", // 所有 agent 可以互相看到 session
    },
  },

  // 绑定: 所有用户消息路由到 main
  bindings: [
    {
      agentId: "main",
      match: { channel: "kimi-claw", accountId: "*" },
    },
  ],
}
```

### 5.2 输出物集中管理

**所有 agent 的产出物写入 `~/.openclaw/workspace/projects/`，Git 管理。**

```
~/.openclaw/workspace/projects/      ← 所有产出物集中在此，Git 管理
├── ma/                              ← 多 Agent 设计文档
├── audit-reports/                   ← auditor 审计报告
├── docs/                            ← publicist 产出（README、总结、随笔）
├── .git/
├── 项目A/
├── 项目B/
├── 项目C/
└── 项目E/

~/.openclaw/workspace-coder/         ← 仅有配置和 session
├── AGENTS.md / SOUL.md / memory/ / temp/

~/.openclaw/workspace-tester/
~/.openclaw/workspace-auditor/
~/.openclaw/workspace-publicist/
        （同上：仅有配置和 session）
```

**Git 规范（所有 agent 遵守）：**
- **新增：** 新文件/目录创建后必须 `git add`，纳入版本管理
- **修改：** 先 `git checkout` 到对应分支再改，不在 main 分支直接修改
- **完成：** 改动完成必须 `git commit`（checkin），写清 commit message
- 不交无法编译的代码，不交没跑过的代码，不交没自审的文档

**Git 工作流（共享目录模式）：**

所有 agent 操作同一目录 `~/.openclaw/workspace/projects/`：

```
main (顾问) 创建任务
    │
    ▼
coder:
  cd ~/.openclaw/workspace/projects
  git checkout -b feature/xxx    ← 从 main 切出 feature 分支
  开发代码
  git add . && git commit -m "实现XXX功能"
  sessions_send → tester: "feature/xxx 已就绪，请测试"
    │
    ▼
tester:
  cd ~/.openclaw/workspace/projects
  git checkout feature/xxx
  运行测试
  ├── 有 bug → sessions_send → coder: "发现bug"
  │   coder 修复 → git commit → tester 重测
  │
  └── 测试通过 → sessions_send → main（附测试报告，main 转交 auditor）
    │
    ▼
auditor:
  cd ~/.openclaw/workspace/projects
  git diff main...feature/xxx   ← 审查所有变更
  git log main..feature/xxx     ← 审查 commit 历史
  安全扫描 + 流程审查
  ├── 有问题 → sessions_send → coder/tester，列出问题清单
  │   coder 修复 → git commit → tester 重测 → auditor 再审
  │
  └── 审计通过 → sessions_send → main:
      "feature/xxx 审计通过，建议合并"
    │
    ▼
main (教练):
  cd ~/.openclaw/workspace/projects
  git diff main...feature/xxx   ← 最终确认
  git checkout main && git merge feature/xxx
  git branch -d feature/xxx
  通知 用户: "功能已完成，已合并到 main"
```

### 5.3 Projects 软链接初始化 🔗

**这是跨 agent 共享项目的关键基础设施。**

由于每个 agent 有独立的 workspace，它们默认看不到 main workspace 下的 projects 目录。
必须为每个 agent workspace 建立软链接：

```bash
# 初始化（新机器或新增 agent 时必须执行）
for ws in workspace-coder workspace-tester workspace-auditor workspace-publicist; do
  target="$HOME/.openclaw/$ws/projects"
  if [ ! -e "$target" ]; then
    ln -s "$HOME/.openclaw/workspace/projects" "$target"
    echo "$ws: projects symlink created"
  else
    echo "$ws: projects already exists"
  fi
done
```

**不建立软链接的后果：** agent 独立 workspace 下没有 projects 目录，
查找项目文件时返回"不存在"错误，导致审计/编码/测试任务失败。
（Linux 硬链接也可以，但软链接更简单、跨文件系统。）

## 6. 教练审查结果 (修订版)

### 审查结论: ✅ 方案可行，守门员模式设计合理

我从教练视角逐项审查修订后的设计：

#### ✅ 核心流程正确
```
coder → tester → auditor 🔒 → main ✅ → 用户
```
- auditor 守住最后一道闸，tester 通过后不直接到 main
- auditor 发现问题报告 main教练，main教练 拥有最终决策权（迭代/接受/放弃）
- 32 分钟完成完整交付流程是正常的——一个环节都不能少
- 每个 agent 有自己的 MEMORY.md，做完项目应更新，沉淀经验
- 用户收到的结果一定是审计通过的
- 这就是真正的四眼原则（实际是六眼：coder+tester+auditor）

#### ✅ 各 Agent 职责清晰
- main: 需求入口 + 结果出口，中间不干预
- coder: 只管实现，不管最终交付
- tester: 只管测试，测试通过 ≠ 交付
- auditor: 只管审计放行，拥有否决权

#### ⚠️ 需要关注的问题

1. **auditor 发现问题的处理流程**
   - auditor 发现问题 → main教练 → main教练 决定是否需要迭代
   - main教练 是唯一的决策者和任务分发者
   - auditor 发现问题时一次性列出全部问题，方便 main教练 决策
   - Git 让审计更精准：auditor 审查的是 `git diff`，而非模糊的"看看代码"

2. **代码共享：共享目录 + Git，零配置** ✅
   - 所有 agent 操作同一 `projects/` 目录
   - Git 自带 `.git/index.lock` 防并发，工作流串行不会冲突
   - auditor 有 exec 权限但 SOUL.md 约束只读代码文件
   - 审计报告写入 `projects/audit-reports/`，纳入 Git 管理

3. **session visibility 的保守策略**
   - `visibility: "all"` 让 auditor 能看到所有 session，包括 main↔用户的私密对话
   - **建议**: 先用 `visibility: "agent"`，auditor 通过 sessions_history 看 coder/tester 的 session
   - 如果需要跨 agent 的 sessions_history，再调整为 `"all"`

4. **模型成本**
   - 四个 agent 各有模型，auditor 也会产生 token 消耗
   - auditor 只在 tester 通过后触发，不会空转——设计合理
   - 预估相比单 agent 增加 2-3x token 消耗

5. **auditor 失败时怎么办？**
   - auditor 模型挂了或超时 → tester 提交审计的消息会得不到回应
   - **建议**: main 的 heartbeat 中检查 "tester 已提交审计但 auditor 无响应超 30min" 的情况
   - 发现后 main 可以重新触发审计或告知 用户

6. **main 的教练角色做什么？**
   - auditor 做了审计，教练做什么？
   - 教练的职责转变为：
     a) 审查 auditor 的审计质量（审计是否足够深入？有没有漏掉的问题？）
     b) 审视全局流程（coder→tester→auditor 整体效率如何？哪些环节慢？）
     c) 偶尔抽查已被审计通过的工作，做二次验证
   - 教练是"对审计者的审计"，不是重复审计

#### 🔧 实施建议

1. **分阶段上线**
   - Phase 1: coder + tester (跳过审计，先跑通 dev-QA 闭环)
   - Phase 2: 加入 auditor (完整的守门员流程)
   - 不要一次性全部上线，每个 phase 验证通过后再加下一个

2. **先跑一个端到端测试**
   - 用一个简单需求走完整流程：main→coder→tester→auditor→main
   - 验证 auditor 能否正确读取 coder/tester 的 session

3. **auditor 的 SOUL.md 需要非常具体**
   - 明确"什么情况下退回"vs"什么情况下放行"
   - 避免 auditor 过于宽松（形同虚设）或过于严格（永远不放行）
   - 给出审计清单模板，确保审计结构化

---

## 7. 最新修订 (2026-05-23)

| # | 修改项 | 内容 |
|---|--------|------|
| 1 | auditor 模型 | deepseek-v4-pro → deepseek-v4-flash（审计不需要深度推理） |
| 2 | auditor 权限 | 取消写限制，开放 coding profile，授权操作 docs/ 目录 |
| 3 | per-project 追踪 | 每个项目 docs/ 下必须有 todo.md（任务登记）+ journey.md（过程记录）+ test-report.md + audit-report.md |
| 4 | 技术栈偏好 | 桌面→Python，Web→JS；**中文优先**，减弱其他语言 |
| 5 | main教练 规格责任 | 每个项目写入 docs/spec.md + docs/style-guide.md + CONTRIBUTING.md，确保迭代时风格统一 |
| 6 | 项目目录规范 | 标准目录：docs/ src/ tests/ scripts/ bin/ lib/ include/（遵循开源项目习惯，按语言裁剪） |
| 7 | auditor AGENTS.md | 从 350 行缩减到 80 行，去掉冗余，恰如其分 |
| 8 | auditor SOUL.md | 从 72 行缩减到 32 行，保留核心人格 |
| 9 | docs 目录 | 所有文档统一放 docs/：spec、design、style-guide、test-report、audit-report、journey、todo |
| 10 | publicist 中文优先 | 强调中文写作，文档和文案均以中文为主 |
| 11 | projects 软链接 | 新增 5.3 节：agent 独立 workspace 必须通过软链接共享 projects 目录 |
| 12 | publicist 必须步骤 | 文档撰写从"可选"改为"必须"；todo.md 模板新增第 5 项任务 |
| 13 | 迁移指南 | 新增 SETUP.md，实现换机器快速重建 MA 环境 |
| 14 | SKILL.md 关联 | multi-agent-design.md 头部添加 skills/team-dev/SKILL.md 关联引用 |
| 15 | 复盘报告 | 新增 RETROSPECTIVE.md，记录 示例项目B 演示的 5 个问题和 9 项行动 |
| 16 | agent 通信 | tester→auditor 改为 main 转手模式（确认设计） |
| 17 | 项目经理角色 | main 四重→五重身份（新增📋 PM）；进度通报增加一句话简报和风险上报模板 |

---

## 8. 已知限制与注意事项

### 8.1 Agent 间通信：main 转手模式

**设计：** tester 不直接 sessions_send 给 auditor，而是通过 main 转手。

tester 完成测试 → sessions_send → main（附测试报告）→ main sessions_spawn auditor

**原因：** 子 agent session 的 sessions_send 缺少 target 上下文（实测）。main 作为唯一入口做 handoff 是可靠方案。每次转手约 30-60 秒额外延迟，不影响正确性。

### 8.2 Projects 软链接

没有软链接，agent 找不到项目文件。**这是新机器部署的#1 陷阱。** 每次团队研发启动前 main 必须检查。

### 8.3 模板同步

projects/ma/ 是模板仓库，agent workspace 是部署目标。修改模板后必须同步到部署。
当前无自动同步机制，需手动执行或定期检查。
