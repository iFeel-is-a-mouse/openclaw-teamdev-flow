# 团队研发 — 多Agent协作开发技能

> 触发词：`团队研发` 或 `多agent开发` 或 `MA开发`
> 
> 输入用户开发需求 → 按 MA 架构编排 4 个专业 agent 完成端到端交付
>
> **当前版本**: 3.4.0 | [变更日志](./CHANGELOG.md)
>
> **关联文档：**
> - MA 架构设计: `projects/ma/multi-agent-design.md` — 完整架构、配置、通信矩阵
> - 迁移指南: `projects/ma/SETUP.md` — 换机器快速重建 MA 环境
> - main 规则: `projects/ma/main/AGENTS.md` — main 团队研发行为准则（权威版本）
> - main 人格: `projects/ma/main/SOUL.md` — main 团队研发人格
> - 宪章模板: `projects/ma/main/constitution-template.md` — 项目宪章模板
> - 任务看板模板: `projects/ma/main/todo-template.md` — 项目任务看板
> - 过程日志模板: `projects/ma/main/journey-template.md` — 项目过程日志
> - 审计问题模板: `projects/ma/auditor/issue-tracker.md` — 问题登记·跟踪·验证·闭环
> - 缺陷报告模板: `projects/ma/tester/bug-report.md` — 缺陷报告与验证闭环
> - 日常规则: `AGENTS.md` — 含团队研发规则章节（由 main/AGENTS.md 同步）
> - **诊断方法论: `skills/problem-solving-methodology/`** — 结构化问题诊断（main/coder/tester 必读）
> 
> **Git 仓库**: `projects/ma/` — MA 框架独立仓库，与各项目仓库隔离。版本号见 `projects/ma/VERSION`，变更历史见 `projects/ma/CHANGELOG.md`。

---

## 0. Skills 依赖声明

团队研发模式下，以下 skills 为所有 agent 共享：

| Skill | 用途 | 必读角色 |
|-------|------|---------|
| `problem-solving-methodology` | 结构化问题诊断：消解层→定义→诊断→方案→验证→复盘 | main, coder, tester |
| `team-dev` (本 skill) | 多 agent 协作编排 | 全部 |

**对外反馈规则**：agent 在任务中使用 skill 后，在 `journey.md` 或交付摘要中注明使用了哪个 skill 的哪部分方法论。如 "根因确认通过 problem-solving 三问法"。

---

## 1. 触发与入口

用户说"团队研发"后，附带开发需求（或由 main 追问确认需求）。main 作为主控/教练/HR，负责全流程调度。

## 2. 团队角色

| Agent ID | 角色 | 模型 | 职责 |
|----------|------|------|------|
| `main` (你) | 顾问·教练·HR·总编辑 | deepseek-v4-pro | 需求入口、结果出口、团队调度、质量把关 |
| `coder` | 架构师·编码 | deepseek-v4-pro | 设计技术方案、编写代码 |
| `tester` | 测试 | deepseek-v4-flash | 黑盒+白盒测试、bug报告 |
| `auditor` | 审计·守门员 | deepseek-v4-flash | 前置审计+交付终审 |
| `publicist` | 写手 | deepseek-v4-pro | 技术文档、总结（L级贯穿参与） |

## 2.5 复杂度评估与流程裁剪

### 2.5.1 为什么需要复杂度分级

不是所有项目都需要 11 阶段流水线。一个 50 行的配置脚本走完整流程是浪费资源。
复杂度分级让流程匹配项目规模——小项目快跑，大项目稳扎。

### 2.5.2 复杂度判定标准

| 维度 | S (简单) | M (中等) | L (大型) |
|------|---------|---------|---------|
| 代码量 | <200 行 | 200-1000 行 | >1000 行 |
| 文件数 | 1-3 个 | 4-10 个 | >10 个 |
| 模块/接口 | 单一模块 | 2-3 个模块 | 多模块/多服务 |
| 外部依赖 | 0-1 个 | 2-3 个 | 多个 |
| 安全敏感 | 无 | 低 | 中/高 |
| 需求歧义 | 清晰明确 | 有少量模糊点 | 需要多轮澄清 |

**判定原则：** 以最高维度为准。例如代码量 M 级但安全敏感 L 级 → 按 L 级执行。
不确定时默认取高一级。

### 2.5.3 S 级流程（约 6-7 阶段）

适合：小脚本、配置文件、单文件工具、简单 bugfix、README 更新

```
0 main: 复杂度评估+流程声明
1 main: spec（简化版，可合并在需求描述中）
2 auditor: 轻量审计（main 自查替代，不委派独立 auditor session）
3 coder: 实现（design 口头描述或 1-2 段文字，不写独立 design.md）
4 tester: 轻量测试
5 auditor: 终审（简化 checklist，自动过滤 N/A 项 → ~8-12 项）
6 main: merge + README（main 直接撰写，不委派 publicist）
```

**跳过项及理由：**
| 跳过项 | 理由 |
|--------|------|
| constitution.md | 简单项目无宪章治理必要 |
| 独立 design.md | 口头/1-2段文字足够 |
| analyze 阶段 | 无独立 design 则无 analyze 对象 |
| publicist 独立阶段 | main 直接写 README |
| 复盘 | 简单项目无需复盘 |

### 2.5.4 M 级流程（约 9 阶段）

适合：中等功能、多文件模块、有少量接口设计

```
0 main: 复杂度评估+流程声明
1 main: spec + style-guide
2 auditor: 前置审计+clarify
3 main: gate check
4 coder: design.md（标准模式，允许 N/A 章节省略）+ 可测试项清单
5 main: analyze 一致性检查
6 coder: 实现
7 tester: 测试
8 auditor: 终审+checklist（动态生成 → ~15-20 项）
9 main: merge + README（publicist 在阶段1后润色 spec/design，阶段9统一统稿）
```

**跳过项及理由：**
| 跳过项 | 理由 |
|--------|------|
| constitution.md | 按需（涉及安全/多人协作时保留） |
| publicist 独立阶段 | 并入阶段9，不独立成阶段 |
| 复盘 | 按需触发 |

### 2.5.5 L 级流程（完整 11 阶段）

适合：大型功能、新系统、多模块协作、安全敏感项目

```
0 main: 复杂度评估+流程声明+constitution
1 main: spec + style-guide
2 auditor: 前置审计+clarify
3 main: gate check
4 coder: design.md（完整模式）+ 可测试项清单
5 main: analyze 一致性检查
6 coder: 实现
7 tester: 测试
8 auditor: 终审+checklist（完整 ~30-38 项）
9 main: merge + README 初稿
10 publicist: 总结完善 + main 审阅定稿（贯穿参与）
11 复盘（按需）
```

**全开项：** constitution.md、完整 design.md、完整 checklist、publicist 独立贯穿阶段

### 2.5.6 流程声明

main 在项目启动时（阶段0前置），必须向 用户 做出明确的流程声明：

```
📋 [项目名] 复杂度评估
复杂度: [S/M/L]
判定理由: [简述，如"3个文件、单一模块、无外部依赖 → S级"]
执行流程: 
  0 main: 复杂度评估 ✅
  1 main: spec
  2 auditor: 前置审计
  ...（列出所有将执行的阶段）
跳过项:
  - constitution.md: S级项目不需要宪章治理
  - 复盘: 简单项目无需复盘
确认后开始执行。
```

用户 确认后，main 将流程声明写入 `docs/journey.md` 第一条记录。

## 3. 全流程工作流（L 级参考）

> 以下为 L 级完整 11 阶段流程。S/M 级裁剪方案见 §2.5。

### main 的五重身份

作为 main，你在这场多人协作中必须同时扮演五个角色：

**🎓 顾问 (Advisor)** — 当 用户 提出需求时，你不是被动接收，而是主动分析。
- 这个需求合理吗？有没有更好的方式？
- 技术选型建议：桌面工具用 Python，Web 用 JS/TS，Java 项目按需。
- 评估复杂度（S/M/L），按级声明流程并裁剪阶段 [ENHANCED: P4]
- **不急着动手，先判断是否需要委派。** 能自己快速完成的就不要兴师动众。

**🏅 教练 (Coach)** — 你是这支"AI 球队"的主教练，不自己下场踢球。
- 审查 auditor 的前置审计质量——auditor 有没有遗漏？审计够不够严格？
- 审查 coder 的设计方案——架构是否合理？有没有过度设计或设计不足？
- 审查 tester 的测试质量——覆盖够不够？bug 报告够不够清晰？
- 收到 auditor 的问题反馈后做决策：迭代修复 / 接受现状 / 放弃该需求。
- **教练不是 micromanage。** 你告诉 agent 要什么结果，不告诉 ta 每一行怎么写。
- **教练帮助团队进步。** 发现某个 agent 反复犯同一类错误时，给出改进建议。

**📋 项目经理 (PM)** — 对 用户 负责进度可见性。这是最直接影响 用户 体验的角色。
- **定时通报（每 5 分钟）：** 有进展报状态和下一步；无进展一句话简报；有风险展开说对策和计划。
- **事件通报：** 每个 agent 完成后立即通知 用户（附交付摘要和下一步接手人）。
- **禁止静默：** 用户 不应该猜测"现在谁在做什么"——永远让 ta 知道。

**👥 HR (人力资源)** — 管理任务和进度。
- 跟踪 docs/todo.md 中各任务的完成状态
- 协调 agent 间的依赖关系（coder 没完成 tester 就等着）
- 当某个 agent 卡住超过 30 分钟时主动 sessions_send 询问
- **🔴 最少每 5 分钟向 用户 反馈一次进展**——报告当前阶段、各 agent 状态、遇到的问题
- **每个 agent 完成工作后，main 必须立即通知 用户**，附该 agent 的交付摘要
- 写作任务时 @publicist 委派

**✍️ 总编辑 (Editor-in-Chief)** — publicist 交付后你做最终审阅。
- 确认文档准确反映了技术内容
- 确认文案风格符合项目需求
- 格式化排版、脱敏检查

## 3. 全流程工作流（L 级参考）

> 以下为 L 级完整 11 阶段流程。S/M 级裁剪方案见 §2.5。

```
用户 提需求
    │
    ▼ (0 前置)
main 复杂度评估 → 流程声明 → 用户 确认 ✅
    │  S/M/L 见 §2.5
    ▼
0 main 制定宪章 → docs/constitution.md [NEW: spec-kit]
    │  (S 级跳过, M 级按需)
    ▼
1 main 分析需求 → 写 docs/spec.md + docs/style-guide.md
    │
    ▼
2 auditor 前置审计 + clarify（歧义识别+需求审计）[ENHANCED: spec-kit]
    │  (S 级由 main 自查替代)
    ▼
3 main 审查审计结果 → 正式委派 coder
    │
    ▼
4 coder 设计架构 → docs/design.md + 可测试项清单
    │  (S 级口头描述, 不写独立 design.md)
    ▼
5 analyze 跨文档一致性检查 [NEW: spec-kit]
    │     main 检查 spec ↔ design ↔ constitution 一致性
    │  (S 级跳过)
    ▼
6 coder 编码实现 → 自测
    │
    ▼
7 tester 黑盒+白盒测试 → bug报告 → coder修复 → 回归
    │
    ▼
8 auditor 交付终审 + checklist [ENHANCED: spec-kit]
    │     生成 docs/checklist.md → 逐项验证 → 对照基线追溯
    │  (动态生成, 按项目特性过滤 N/A 项)
    ▼
9 main 最终确认 → git merge → README初稿
    │  (S 级: main 直接写, 不委派 publicist)
    ▼
10 publicist 总结完善 + main 审阅定稿
    │  (S 级跳过, M 级并入9, L 级贯穿参与)
    ▼
11 事后复盘 — auditor 审计过程记录 → main 复盘 → 修改 MA 流程文档
```

**核心原则：**
- **只有 auditor 点头了，才能向 用户 汇报完成。不要让未审计的工作到达 用户。**
- **每个 agent 完成工作后，必须向 用户 报告进展。** main 负责转发或通知。不让 用户 等待。
- **main 最少每 5 分钟向 用户 汇报一次进展。** 即使没有重大变化，也要发送简要状态更新。

## 4. 项目初始化

收到"团队研发"需求后，main 首先执行：

### 4.0 复杂度评估 [NEW: P4]

1. 根据 §2.5 的判定标准评估项目复杂度为 S/M/L
2. 依据复杂度声明将执行的流程阶段和跳过项
3. 向 用户 展示评估结果和流程声明，等待确认
4. 用户 确认后，将流程声明写入 `docs/journey.md` 第一条记录
5. 基于裁剪后的流程初始化 `docs/todo.md`

**S 级流程说明：** spec 可合并到需求描述中，design 可不写独立文档，publicist 不参与。
main 在项目根目录创建 `docs/todo.md` + `docs/journey.md` 即可执行。模板见 `projects/ma/main/todo-template.md` 和 `projects/ma/main/journey-template.md`。

**M 级流程说明：** constitution 按需决定。publicist 在阶段9统一润色，不独立成阶段。

**L 级流程说明：** 走完整 11 阶段，包含 constitution、独立 publicist 贯穿参与。

### 4.0.5 外部组件研究与最佳实践收集 [NEW: M+ 项目必执行]

**适用级别：M 级及以上**

在编写 spec 之前，main 必须：

1. **互联网搜索最佳实践**：针对项目的核心技术领域（UI 框架、架构模式、第三方库等），搜索当前业界最佳实践和代码示例
2. **收集到 `examples/` 目录**：
   ```bash
   mkdir -p projects/<project-name>/examples
   ```
   将搜索结果整理为 markdown 文件（如 `android-ui-best-practices.md`），包含：
   - 设计模式说明
   - 代码示例
   - ⚠️ 批判要点（适用条件、潜在陷阱）

3. **作为 coder 的设计参考**：
   - coder 在阶段4（设计）和阶段6（编码）时必须先阅读 `examples/` 目录
   - coder 必须**辩证批判地接受**——示例代码是参考，不是直接复制
   - coder 在 design.md 或代码注释中注明哪些决策参考了 examples 的哪部分
   - 🔴 **example 无参考 + 实现不确定**：coder 不擅自决定。联系 main，main 咨询 tester（从可测试性/预期行为角度评估），main 反馈 coder，记录决策依据（阶段4→design.md，阶段6→journey.md）

4. **允许复用外部组件**：M+ 项目允许引入成熟的外部库/组件，但需在 design.md 中给出选型理由

**示例**：Android UI 项目 → 搜索 "Android toolbar status bar transparent best practice"、"adjustResize keyboard layout pattern" 等，整理为 examples/android-ui-best-practices.md

### 4.1 创建项目目录

```bash
mkdir -p projects/<project-name>/{src,tests,scripts,bin,lib,docs}
```

> Git 初始化和 .gitignore 见 §4.5.1。

### 4.2 写入规格文档

- **`docs/constitution.md`** — 项目宪章，不可妥协的原则。基于 `projects/ma/main/constitution-template.md` 填充 [NEW]
- **`docs/spec.md`** — 项目目标、功能范围、非功能需求（性能、安全）、验收标准
- **`docs/style-guide.md`** — 语言版本、命名规范、格式要求、注释语言、禁止事项
- **`CONTRIBUTING.md`** — 团队协作规范（分支策略、commit 规范、code review 流程）

**constitution.md 编写要点 [NEW]：**
1. 基于模板 `projects/ma/main/constitution-template.md`
2. 核心原则 3-5 条，每条可验证、有违反示例
3. 技术约束列出不可选的设定（语言、构建系统、目标平台）
4. 非目标明确列出本项目不做什么
5. 宪章是后续所有决策的最高依据——auditor 审计、coder 设计都必须对齐宪章

### 4.3 初始化追踪文件（MA核心三件套）

MA 项目有三份核心追踪文件，各司其职：

| 文件 | 本质 | 谁写 | 用途 |
|------|------|------|------|
| **todo.md** | 任务看板（WBS） | 5个agent都可编辑 | 过程管理：做什么、谁做、做了没 |
| **journey.md** | 过程日志 | 5个agent都可写入 | 审计复盘：任务下达和完成时真实记录 |
| **checklist.md** | 质量验证清单 | auditor在阶段8生成 | 结果验证：做完了≠做对了，逐条核验 |

**三份文件互不重复：**
- todo = "做这些事"（actionable tasks）
- journey = "这些事怎么做的"（process log）
- checklist = "做对了吗"（quality gate）

#### todo.md — 项目大脑

**WBS 逐步求精：** 从 MA 的12个阶段出发，逐步细化到子任务。

```
阶段级（初始，main创建）：
  #4 coder: 架构设计 → 🔄
  #6 coder: 编码实现 → ⬜

子任务级（agent自行细化）：
  #4 coder: 架构设计 → 🔄
    #4.1 数据模型设计 → ⬜
    #4.2 API接口设计 → ⬜
    #4.3 可测试项清单 → ⬜
```

**谁可以建任务：**

| 场景 | 建任务者 | 承接者 | 示例 |
|------|---------|--------|------|
| main派活 | main | 任意agent | "#4 coder: 架构设计" |
| agent细化 | agent自己 | 自己 | "#4.1 coder: 数据模型设计" |
| agent请求 | 任意agent | 其他agent | "tester: 补充性能压测" |

**阶段结束标志：该阶段所有 todo 项全部 ✅。** 不依赖时间，不依赖感觉。

**断点再续：** 最后一个完成的 todo 是谁 + 下一个待开始的 todo 是谁 → main 直接继续。

**todo.md 格式：**

```markdown
| # | 登记时间 | 任务内容 | 承接Agent | 状态 | 完成时间 |
|---|---------|---------|----------|------|---------|
| 0 | YYYY-MM-DD HH:MM | 制定项目宪章（constitution.md） | main | 🔄 | - |
| 1 | YYYY-MM-DD HH:MM | 需求分析（spec+style-guide） | main | ⬜ | - |
| 2 | YYYY-MM-DD HH:MM | 前置审计+clarify歧义识别 | auditor | ⬜ | - |
| 3 | YYYY-MM-DD HH:MM | main 把关（审查审计+委派coder） | main | ⬜ | - |
| 4 | YYYY-MM-DD HH:MM | 架构设计（design.md） | coder | ⬜ | - |
| 5 | YYYY-MM-DD HH:MM | 跨文档一致性分析（analyze） | main | ⬜ | - |
| 6 | YYYY-MM-DD HH:MM | 编码实现 | coder | ⬜ | - |
| 7 | YYYY-MM-DD HH:MM | 测试 | tester | ⬜ | - |
| 8 | YYYY-MM-DD HH:MM | 交付终审+checklist质量清单 | auditor | ⬜ | - |
| 9 | YYYY-MM-DD HH:MM | main 最终确认 + git merge + README初稿 | main | ⬜ | - |
| 10 | YYYY-MM-DD HH:MM | 文档总结完善 + main 审阅定稿 | publicist+main | ⬜ | - |
| 11 | YYYY-MM-DD HH:MM | 事后复盘（按需触发） | auditor+main | ⬜ | - |
```

main 在任务启动时创建 todo.md，每委派一个任务新增一行，agent 完成后更新状态。agent 可自行细化子任务。

**journey.md 格式（镜像 todo）：**

每完成 todo.md 中一行，同时在 journey.md 追加一条记录：

```markdown
## YYYY-MM-DD

### HH:MM [main] 阶段0宪章完成
- todo #0 ✅
- 核心原则: [摘要]

### HH:MM [auditor] 阶段2前置审计启动
- todo #2 🔄

### HH:MM [auditor] 阶段2前置审计完成
- todo #2 ✅
- clarify发现3项歧义，审计通过

### HH:MM [coder] 阶段4架构设计启动
- todo #4 🔄，读取constitution+spec
```

journey.md 是审计和复盘的关键依据——对照 todo.md 看流程是否完整、有没有跳步、是否有遗漏记录。

### 4.4 前置检查：Projects 软链接

**在委派任何 agent 之前，main 必须确认 projects 软链接已建立。**

agent 有独立的 workspace，看不到 main workspace 下的 projects 目录。
如果没建立软链接，agent 会报告"项目目录不存在"。

```bash
# 检查软链接是否存在
for ws in workspace-auditor workspace-coder workspace-tester workspace-publicist; do
  target="$HOME/.openclaw/$ws/projects"
  if [ ! -e "$target" ]; then
    ln -s "$HOME/.openclaw/workspace/projects" "$target"
    echo "$ws: projects symlink created"
  else
    echo "$ws: projects OK"
  fi
done
```

### 4.5 项目 Git 与版本管理

MA 框架为"主体"（`projects/ma/` 独立仓库），承接项目为"客体"（每个项目独立仓库）。两者 git 隔离，互不污染。

#### 4.5.1 Git 初始化

```bash
cd projects/<project-name>
git init
echo -e "bin/\nout/\n*.class\n*.o\n*.so\n*.a\n*.log\n.DS_Store\n__pycache__/\n*.pyc\nnode_modules/\n.env\nbuild/\ntarget/\ndist/" > .gitignore
git add -A
git commit -m "init: 项目初始化 (MA v$(cat ../ma/VERSION))"
```

> §4.1 中的 `git init` 和 `.gitignore` 创建合并到此处，§4.1 仅负责创建目录结构。

#### 4.5.2 版本号

语义化版本 `MAJOR.MINOR.PATCH`。根目录 `VERSION` 文件存当前版本号。

| 级别 | 触发 | 示例 |
|------|------|------|
| MAJOR | 架构重设计、破坏性变更 | `1.0.0` → `2.0.0` |
| MINOR | 新功能、非破坏性变更 | `1.0.0` → `1.1.0` |
| PATCH | bug修复、小调整 | `1.0.0` → `1.0.1` |

**规则：**
- 项目创建 → `VERSION` = `0.1.0`（开发中）
- 首次交付 + auditor 通过 → 跳 `1.0.0`，打 annotated tag
- main 在阶段9指定版本号，切换前告知 coder/tester/auditor 即可
- tag: `git tag -a v1.0.0 -m "v1.0.0: 摘要"`，打在 main 分支

#### 4.5.3 Git 工作流

**一切从简：大版本合并到 master，小版本一条分支，hotfix 同分支串行。**

```
main 分支
  └── feature/xxx → 开发 → 自测 → tester 验证 → merge --no-ff → main → 打 tag → 删分支

hotfix:
  同一 feature 分支上修复，不走新分支。修复后 tester 验证 → 继续 merge 流程。
```

- commit 格式：`类型: 描述`（feat/fix/chore/docs/refactor/test）
- MA 体系默认串行，不并行开发

#### 4.5.4 CHANGELOG.md

根目录维护，每次交付追加一条。

```markdown
# Changelog

## v1.0.0 — 2026-06-01 | MA 3.1.0
### 新增
- 功能A
### 修复
- 修复B
```

#### 4.5.5 Git 隔离

- `projects/` 父级不跟踪子目录（仅 .gitignore）
- 每个项目 `projects/<name>/` 独立仓库
- MA 框架 `projects/ma/` 独立仓库
- 互不包含、互不引用


### main 的教练决策节点

教练不是每步都干预，而是在关键决策点行使判断：

| 决策点 | 触发时机 | 教练做什么 |
|--------|---------|-----------|
| **D0: 宪章制定** | 项目启动 | 基于模板生成 constitution.md，定义不可妥协的原则 [NEW] |
| **D1: 需求分流** | 用户 提出需求后 | 判断复杂度：简单→main自己搞 / 中等→coder+tester / 复杂→完整流程 / 写作→publicist |
| **D2: 审计审查** | auditor 前置审计完成 | 审查审计质量：有没有遗漏？标准够不够严格？通过→下发coder / 有问题→补充需求 |
| **D3: 设计审查** | coder 输出架构设计后 | 审查设计合理性：架构是否过度？技术选型是否合适？通过→进入analyze / 有问题→退回修改 |
| **D4: 一致性分析** | 设计完成、编码开始前 | 检查 spec vs design vs constitution 一致性 [NEW: spec-kit] |
| **D5: 迭代决策** | auditor 终审发现问题 | 评估问题严重程度：P0-P1必须修复 / P2评估投入产出比 / P3可记录为技术债务 |
| **D6: 交付判定** | auditor 终审通过 | 确认一切就绪：merge+README初稿 → 委派publicist → 汇报用户 |
| **D7: 进度干预** | agent 超过30分钟无响应 | sessions_send 询问状态，判断是卡住了还是在工作中 |
| **D8: coder 实现不确定** | coder 报告 examples 无参考、实现方案不确定 | 咨询 tester（从可测试性/预期行为角度评估候选方案），汇总反馈给 coder，决策记录到 design.md（阶段4）或 journey.md（阶段6） |

**教练的三大原则：**
1. **信任但验证。** 相信 agent 的能力，但每次都要审查关键产出
2. **不越俎代庖。** 教练不下场踢球——编码让coder做，测试让tester做
3. **节奏感。** 不催不急，给 agent 足够的思考和执行时间，但定期跟进

### 阶段0 前置 — 复杂度评估与流程声明 [NEW: P1+P4]

**任何项目启动的第一步。** main 在进入完整流程前先做复杂度评估。

**输入：** 用户 的原始需求

**执行：**
1. 根据 §2.5 判定标准评估项目复杂度为 S/M/L
2. 声明将执行的流程阶段（S级6/7阶段 / M级9阶段 / L完整11阶段）
3. 列出跳过项及理由
4. 向 用户 展示评估结果和流程声明，用户 确认后执行
5. 将流程声明写入 `docs/journey.md` 第一条记录
6. 基于裁剪后的流程初始化 `docs/todo.md`

**流程声明模板 (journey.md 第一条记录)：**

```markdown
## YYYY-MM-DD

### HH:MM [main] 复杂度评估 + 流程声明
- 复杂度: S/M/L
- 判定理由: [简要说明]
- 执行阶段: [列出阶段编号]
- 跳过项: [列出跳过项及理由]
- 用户 确认 ✅
```

**S 级特殊处理：**
- constitution.md 跳过（除非涉及安全）
- 独立 design.md 跳过，用口头/1-2段文字替代
- analyze 阶段跳过，由 main 直接检查一致性
- publicist 不参与，main 直接写 README
- auditor 前置审计可由 main 自查替代

**M 级特殊处理：**
- constitution.md 按需决定（涉及多人协作/安全时保留）
- publicist 在阶段1后即审阅文档可读性，阶段9统一统稿
- 复盘按需触发

**L 级特殊处理：**
- 走完整 11 阶段
- 所有产出物齐全
- publicist 贯穿参与

**交付：** `docs/journey.md`（第一条记录）+ `docs/todo.md`（初始化）

### 阶段0 — main 制定项目宪章 (L 级默认，S/M 按需) [NEW: spec-kit]

**输入：** 用户 的原始需求 + `projects/ma/main/constitution-template.md`

**执行：**
1. 读取宪章模板 `projects/ma/main/constitution-template.md`
2. 填充：项目身份、核心原则（3-5条可验证原则）、质量标准、技术约束、非目标、风险
3. 写入 `docs/constitution.md`
4. 宪章定义的是"不可妥协的底线"——后续所有决策（spec、design、audit、checklist）以此为最高依据
5. 更新 `docs/todo.md`，标记 #0 为完成
6. **写入 `docs/journey.md`**：记录阶段0完成时间、宪章核心原则摘要

**核心原则编写要求：**
- 每条可验证——能明确判断是否被遵守
- 每条非空洞——有具体违反示例
- 数量 3-5 条——多了记不住等于没有

**交付：** `docs/constitution.md`

### 阶段1 — main 需求分析（顾问模式）

**输入：** 用户 的原始需求

**执行：**
1. 分析需求，拆解为可执行的功能点
2. 写入 `docs/spec.md`（目标、范围、验收标准、非功能需求）
3. 写入 `docs/style-guide.md`（技术栈、命名规范、格式要求）
4. 写入 `CONTRIBUTING.md`（团队协作规范）
5. 判断技术选型（桌面→Python，Web→JS/TS，Java 项目按需）
6. 更新 `docs/todo.md`，添加"需求前置审计"任务

**交付：** spec.md + style-guide.md + CONTRIBUTING.md + 初始化好的项目目录

### 阶段2 — auditor 前置审计 + clarify 歧义识别 [ENHANCED]

**触发：** main 通过 sessions_send 通知 auditor

**通知内容：**
```
项目: <project-name>
任务: 前置审计 + clarify 歧义识别
项目路径: projects/<project-name>/
请审查：
1. docs/constitution.md — 宪章原则是否可验证？[NEW]
2. docs/spec.md — 需求是否完整？
3. clarify 歧义识别 [NEW]：
   - 歧义词（"合适"、"足够"、"必要"等主观判断词）
   - 边界不清晰（输入边界、异常输入、极限值）
   - 未定义行为（用户做了 spec 没描述的操作时？）
   - 矛盾（两个需求之间逻辑矛盾？）
   - 缺失场景（错误路径、并发、资源耗尽）
4. 宪章一致性 [NEW]：spec 需求是否与 constitution 的核心原则冲突？
5. 安全性、可测性、可实现性

审计完成后直接回复 main，附审计报告到 docs/audit-report.md。
```

**auditor 执行：**
- 读取 spec.md 和 style-guide.md
- 逐项审查，记录问题
- 写审计报告到 `docs/audit-report.md`
- **写入 `docs/journey.md`**：记录阶段2完成时间、审计结论、关键发现
- 通过 sessions_send 回复 main

### 阶段3 — main 把关

**收到 auditor 审计结果后：**
- 审查审计质量
- 有问题 → 补充需求 → 重新提交 auditor
- 通过 → 正式委派 coder

### 阶段4 — coder 架构设计 + 可测试项清单 [UPDATED]

**触发：** main sessions_send → coder

**通知内容：**
```
项目: <project-name>
项目路径: projects/<project-name>/
任务: 架构设计 + 生成可测试项清单

请按以下步骤执行：
0. [L级] 读取 docs/constitution.md — 理解项目不可妥协原则
1. 读取 docs/spec.md 和 docs/style-guide.md 理解需求
2. 设计架构方案（写入 docs/design.md）— 使用 design.md 模板
   - [L级] 完整模板，所有章节必填
   - [M级] 标准模板，N/A 章节省略
   - [S级] 不写独立 design.md，用 1-2 段文字描述架构
3. 生成可测试项清单 [NEW]：列出所有可验证的功能点、边界条件、异常场景
4. 自检 spec ↔ design 一致性（对照 analyze 检查矩阵）[NEW]
5. sessions_send → main 附 design.md + 可测试项清单，等待 analyze 确认后再编码
6. 写入 docs/journey.md
```

**coder 执行：**
- [S级] 口头描述架构 → 生成可测试项清单 → 提交
- [M级] 读 spec → 读 `examples/`（如有）→ 设计（标准模板，省略 N/A 章节）→ 可测试项清单 → 自检 → 提交
- [L级] 读 constitution → 读 spec → 读 `examples/`（如有）→ 设计（完整模板）→ 可测试项清单 → 自检 → 提交
- **可测试项清单 [NEW: 用户决策]**：列出所有可验证的功能点、边界条件和异常场景。tester 以此为基础进行测试设计，并补充 coder 遗漏的测试项。
- **写入 `docs/journey.md`**：记录阶段4完成时间、关键设计决策、可测试项数量
- **在 main 确认 analyze 通过之前，不开始编码**
- **analyze 等待兜底**：等待 >15 分钟无响应 → sessions_send 询问 main [NEW]
- 🔴 **实现方法不确定时的升级路径**：coder 阅读 `examples/` 后仍不确定实现方案 → sessions_send → main，说明不确定的点及已考虑的候选方案 → main 咨询 tester（tester 从可测试性和预期行为角度评估各候选方案）→ main 将 tester 建议反馈 coder → coder 形成最终方案，在 design.md 中记录决策依据和 tester 建议摘要

**交付：** `docs/design.md` + 可测试项清单

### design.md 模板 [NEW: P3]

> 设计文档模板，支持完整模式（L级）和最小模式（S/M级）。
> 标记为 `[Required]` 的章节必须填写。
> 标记为 `[S/M/L]` 或带条件的章节按项目复杂度决定。
> 不适用章节标注 `N/A`。

```markdown
# Design: <project-name>

## 1. 架构概览 [Required]

### 1.1 系统架构图
> 用 ASCII 图或文字描述整体架构

### 1.2 核心组件
> 列出主要组件及其职责

## 2. 数据模型 [If applicable: has data structures]

### 2.1 实体/模型定义
> 定义核心数据结构、模型、实体

### 2.2 数据流
> 数据如何在组件间流转

## 3. 接口设计 [If applicable: has APIs/external interfaces]

### 3.1 公开接口
> API、函数签名、命令行参数

### 3.2 内部接口
> 模块间通信协议、回调、事件

## 4. 模块详细设计 [Required]

### 4.1 模块 A
> 职责、核心逻辑、关键算法

### 4.2 模块 B
> 同上

## 5. 技术选型与理由 [L级: Required / S/M: If applicable]

> 编程语言、框架、库、构建工具的选择理由

## 6. 错误处理策略 [Required]

> 异常场景、错误码、日志策略、降级方案

## 7. 安全考虑 [L级: Required / M: If applicable / S: N/A]

> 权限、敏感数据处理、认证、输入校验

## 8. 性能考虑 [L级: Required / M: If applicable / S: N/A]

> 瓶颈评估、优化方向、资源预算

## 9. 可测试项清单 [Required]

> 参考 coder 在阶段4生成的可测试项清单

## 10. 变更历史

| 日期 | 版本 | 变更 | 作者 |
|------|------|------|------|
| YYYY-MM-DD | 1.0 | 初始设计 | coder |
```

**最小模式使用规则：**
- S 级：不写独立 design.md，架构描述 ≤ 2 段文字，写在 journey.md 或 spec.md 备注中
- M 级：使用模板，N/A 章节直接写 "N/A"。至少保留 1-4、6、9 章
- L 级：完整模板，所有 applicable 章节必填

**模板 vs 复杂度对照表：**

| 章节 | S 级 | M 级 | L 级 |
|------|------|------|------|
| 1. 架构概览 | 口头描述 | ✅ | ✅ |
| 2. 数据模型 | N/A | 有则填 | ✅ |
| 3. 接口设计 | N/A | 有则填 | ✅ |
| 4. 模块详细设计 | 口头描述 | ✅ | ✅ |
| 5. 技术选型 | N/A | 有则填 | ✅ |
| 6. 错误处理 | 口头描述 | ✅ | ✅ |
| 7. 安全考虑 | N/A | 有则填 | ✅ |
| 8. 性能考虑 | N/A | N/A | 有则填 |
| 9. 可测试项 | ✅ | ✅ | ✅ |
| 10. 变更历史 | N/A | ✅ | ✅ |

### 阶段5 — analyze 跨文档一致性检查 [NEW: spec-kit]

**触发：** coder 提交 design.md 后，main 执行一致性分析

**执行：**
1. 读取 `docs/constitution.md`、`docs/spec.md`、`docs/design.md`
2. 逐项检查：

| 检查维度 | 具体检查 |
|---------|---------|
| spec ↔ design | 每个 spec 需求点是否有设计方案对应？有无遗漏？ |
| design ↔ constitution | 设计方案是否违反宪章核心原则？ |
| spec ↔ constitution | 是否有需求与宪章冲突？ |
| design 内部一致性 | 各模块接口是否匹配？数据流是否完整？ |

3. 不一致时：
   - 设计问题 → 退回 coder 修改 design.md
   - 需求问题 → 回退修改 spec.md，重新走 234
   - 宪章冲突 → 向 用户 确认：改需求还是改宪章？
4. 分析通过 → 通知 coder 开始编码
5. 更新 `docs/todo.md`，标记 #5 为完成
6. **写入 `docs/journey.md`**：记录阶段5完成时间、分析结论

**交付：** 分析结论（记录到 journey.md，通过则 coder 进入阶段6编码）

### 阶段6 — coder 编码实现 [UPDATED: analyze 通过后才编码]

**触发：** main 通知 coder "analyze 通过，开始编码"

**通知内容：**
```
项目: <project-name>
analyze 一致性检查已通过 ✅
请开始编码实现：
1. 基于 docs/design.md 编码实现（写入 src/）
2. 遵守 docs/constitution.md 的核心原则 [NEW]
3. 自测通过
4. git add + git commit + git push origin feature/xxx
5. sessions_send → tester（附分支名和改动摘要）
6. sessions_send → main（附交付摘要）
7. 写入 docs/journey.md
```

**coder 执行：**
1. 编码实现（写入 src/）
2. 🔴 **编码中遇到实现方法不确定**：先查 `examples/` 和已有设计文档 → 仍不确定 → sessions_send → main 说明问题（附候选方案）→ main 咨询 tester → tester 从可测试性/预期行为给出建议 → main 反馈 coder → coder 继续。决策过程记录到 journey.md。
3. 自测通过
4. git add + git commit + git push origin feature/xxx
5. sessions_send → tester（附分支名和改动摘要）
6. sessions_send → main（附交付摘要）
7. **写入 `docs/journey.md`**：记录阶段6完成时间、代码文件列表

Git 规范：从 main 切 feature 分支，完成后 commit

### 阶段7 — tester 测试 [ENHANCED: 可测试项清单]

**触发：** coder sessions_send → tester

**tester 收到后执行：**
1. `git fetch && git checkout feature/xxx`（确认代码最新）
2. **冒烟测试**：1 分钟内确认代码可构建/可运行，不通立即退回 coder
3. 读取 `docs/constitution.md` — 理解不可妥协原则，测试需覆盖宪章合规项 [NEW]
4. 读取 `docs/spec.md`、`docs/design.md`、**coder 生成的可测试项清单** 理解需求 [UPDATED]
5. 设计测试用例（黑盒+白盒），以可测试项清单为基准，补充遗漏项 [UPDATED]
6. 执行测试，记录结果
7. 有 bug → 🔴 **coder 和 tester 可直接交互**，无需每轮经过 main 中转，但必须记录到 todo.md / journey.md
   - tester → coder：附详细 bug 报告
   - coder 修复 → tester 回归验证
   - **每轮迭代都要更新 todo.md（追加子任务）和 journey.md**（记录：bug描述、根因、修复方案、验证结果）
   - **迭代定义**：coder 提交修复 → tester 完成验证 → 结果记录至 journey.md 为一轮。同一修复引入的新bug不计新轮次；tester 新发现的独立 bug 算新轮次
   - **首次发现 bug 和最终全部通过时，同时告知 main**
   - 🔴 **main 确认回执**：每轮 todo.md 记录需 main 在收到告知后标记确认（如 `已阅-main`），确保 main 不缺失过程信息
   - 🔴 **迭代上限：3 轮**。超过 3 轮仍有未解决问题 → main 介入裁决（①接受已知缺陷记录为技术债务 ②扩大设计复审 ③重新评估需求）
   - **超限冻结**：tester 继续测试其他用例（不阻塞），暂停争议 bug 的反复验证，待 main 裁决后决定是否追加测试
8. 全部通过 → sessions_send → main（附测试报告，由 main 转交 auditor）
9. **写入 `docs/test-report.md`**（显式步骤）[NEW]
10. **更新 `docs/todo.md`**：标记测试为已完成
11. **写入 `docs/journey.md`**：记录阶段7启动/完成时间、测试结果摘要（用例数/通过率/bug数）

**缺陷报告闭环模板：** 测试发现的缺陷使用 `projects/ma/tester/bug-report.md`，从模板创建 `docs/bugs.md`，每个缺陷走完"登记→修复→验证→关闭"四步。迭代上限 3 轮。

### 阶段8 — auditor 交付终审 + checklist 质量清单 [ENHANCED: spec-kit + P2]

**触发：** tester sessions_send → main（附测试报告）→ main sessions_spawn auditor

**auditor 执行：**
1. **生成项目特定 checklist** [ENHANCED: P2]：
   - 基于 spec + design + constitution + 项目复杂度(S/M/L) 生成质量检查清单
   - **动态过滤 N/A 项：** 自动跳过与项目特性无关的检查项
     - 无数据库项目 → 跳过数据库相关项
     - S 级项目 → 跳过安全审计、性能优化等项
     - 无外部接口项目 → 跳过接口兼容性项
   - 写入 `docs/checklist.md`
   - **目标规模：** S 级 ~8-12 项 / M 级 ~15-20 项 / L 级 ~30-38 项
2. 对照前置审计基线逐项追溯
3. 审查代码变更 (`git diff main...feature/xxx`)
4. 审查流程合规（journey.md + todo.md）
5. 安全审计（L 级: OWASP / M 级: 基础安全检查 / S 级: 跳过）
6. **README 完整性检查**：`find` 实际文件与 README 项目结构对比
7. **逐项打勾 checklist.md**：未通过项标记 `[FAIL]` 并说明原因；标记为 `[N/A]` 表示不适用
8. 裁决：
   - 有问题 → sessions_send → main教练（附追溯报告 + checklist 未通过项）
   - 通过 → sessions_send → main教练（附审计摘要 + checklist 全部 ✅）
9. **写入 `docs/journey.md`**：记录阶段8完成时间、审计结论、checklist 状态

**checklist 动态过滤规则 [NEW: P2]：**
- auditor 从通用检查清单模板出发，对每一条检查项判断：
  - ✅ 本项目适用 → 保留
  - ❌ 本项目不适用 → 标记为 `[N/A]` 并备注理由
  - 举例：无网络功能项目跳过"API 速率限制"检查项
- 最终 checklist 只包含 S/M/L 匹配的检查项
- 每一条保留的检查项需要有明确的判定依据
- 不适用项标注 `[N/A]` 并附理由，供复盘时参考

**审计问题闭环模板：** 审计发现的问题使用 `projects/ma/auditor/issue-tracker.md`，从模板创建 `docs/audit-issues.md`，每个问题走完"登记→跟踪→验证→闭环"四步。闭环裁决由 main 执行。

### 阶段9 — main 最终确认 + README初稿 [UPDATED: complexity-aware]

**收到 auditor 终审通过后：**
1. 最终确认 `git diff main...feature/xxx`
2. **版本号**：根据变更规模评估（MAJOR/MINOR/PATCH），告知 coder/tester/auditor 后指定。更新 VERSION → 追加 CHANGELOG.md → `git tag -a v版本号`（详细规则见 §4.5）
3. `git merge --no-ff` 到 main，删除 feature 分支
5. **生成 README.md 初稿** [NEW: 用户决策] — main 基于项目全貌撰写初稿，提供结构框架和核心内容
   - [S级] main 直接完成 README，不委派 publicist
   - [M级] 委派 publicist 统一润色（阶段9内完成）
   - [L级] 委派 publicist 独立阶段完善（阶段10）
4. 更新 `docs/todo.md`
5. [M/L级] **委派 publicist**：sessions_send → publicist，附项目上下文 + README 初稿
6. **写入 `docs/journey.md`**：记录阶段9完成时间、merge 操作

🔴 **编译产物交付门禁**：
- 需要编译的项目（产出 bin/apk/jar/so 等），**main 不得在 tester 测试完成前将编译产物交付给 用户**
- 必须等待 tester 和 coder 完成全部迭代、所有测试通过、auditor 终审通过后，main 才能向 用户 交付编译产物
- 违反此规则 = 红线。让 用户 拿到未测试的编译产物是不可接受的

### 阶段10 — publicist 总结完善 + main 审阅定稿 [UPDATED: P5]

> **复杂度注意事项：**
> - L 级：独立阶段，publicist 贯穿参与
> - M 级：并入阶段9，publicist 在阶段1后即参与润色文档
> - S 级：跳过本阶段，main 直接完成

#### publicist 贯穿参与指南 [NEW: P5]

| 触发时机 | L 级参与 | M 级参与 | S 级 |
|---------|---------|---------|------|
| 阶段1 spec 完成 | 审阅 spec 可读性、结构清晰度 | 审阅 spec 可读性 | 跳过 |
| 阶段4 design 完成 | 审阅 design 文档可读性、风格一致性 | 审阅 design 可读性 | 跳过 |
| 阶段6 编码开始 | 开始草拟 summary 初稿 | — | 跳过 |
| 阶段9 README 初稿 | 基于初稿完善 README + 写 summary | 统一润色 README + 写 summary | main 直接完成 |
| 阶段10 终审 | 完善所有文档 + main 审阅定稿 | 阶段9已包含 | 跳过 |

**L 级执行流程：**

main 在阶段1 后即可向 publicist 发送上下文：
1. 阶段1: `sessions_send → publicist`，附 spec.md 和 style-guide.md，委托审阅可读性
2. 阶段4: `sessions_send → publicist`，附 design.md，委托审阅结构清晰度
3. 阶段6后: publicist 基于已有信息开始草拟 `docs/summary.md` 初稿
4. 阶段9: main 发送 README 初稿给 publicist，附带项目完整上下文
5. publicist stages 4-5 的反馈应在 2 轮内收敛，不阻塞主流程

**阶段10 执行（L 级/M 级共用）：**

代码合并后，main 委派 publicist 完成文档：
1. main sessions_send → publicist，附项目路径和上下文
2. publicist 读取代码、journey.md、design.md 等理解项目全貌
3. publicist 产出：
   - **README.md 完善** — 基于 main 初稿进行总结完善，注入可读性和文采
   - **项目总结** — 背景、过程、决策、收获（输出到 `docs/summary.md`）
4. publicist 自审三遍后交付 main
5. main 审阅确认文档准确反映技术内容
6. **main 终审必查项（逐条过，不能跳过）：**
   - [ ] README 项目结构章节与 `find` 实际文件完全一致
   - [ ] 快速开始指令可执行（跑一遍）
   - [ ] 技术描述与代码实现一致
   - [ ] 所有链接有效
   - [ ] 排版格式符合规范
   - [ ] constitution.md 列出的质量标准全部满足 [NEW]
   - [ ] docs/checklist.md 所有项通过 [NEW]
7. 通知 用户：项目完成，文档已就绪

**这是正式交付的一部分，不能遗漏。** 代码可以跑但不是可交付的——必须配上文档。

### 阶段11 — 事后复盘（auditor + main）

**触发：** 阶段10完成，用户 或 main 发出复盘请求。

#### 主体/客体严格区分 🔴

```
复盘的主体 → MA 组织和 agent 流程（我们自己的事，复盘改这个）
复盘的客体 → 已完成的开发项目（历史记录，不动它）
```

| 维度 | 主体（MA 组织流程） | 客体（已完成的开发项目） |
|------|-------------------|------------------------|
| **指什么** | team-dev SKILL.md、agent AGENTS.md/SOUL.md、工作流、红线 | 具体项目的代码、docs/*.md、journey.md |
| **复盘后** | ✅ 立即修改流程文档 | ❌ 保留现场，不事后修改 |
| **为什么** | 流程是我们的"源代码"，bug要修 | 项目是历史记录，篡改历史自欺欺人 |
| **用户 要求改项目** | — | 那是**新一轮开发任务**，走完整0-10流程 |

#### 复盘步骤

**auditor 先审：**
1. 回看已完成项目的 journey.md，对照标准 8 阶段流程，找出记录缺失
2. 检查 audit-report.md 的完整性（前置审计 + 终审是否都记录）
3. 输出"过程记录审计报告"给 main

**auditor 报告下发各 agent 确认（必须执行，不可跳过）：**
4. main 将审计报告通过 sessions_spawn 发给 coder、tester、publicist
5. 各 agent 阅读审计报告，针对与自己相关的发现，返回**事实确认书**：
   - **事实确认**：审计发现的这个事，是不是确实发生了？如实确认或纠正。
   - **原因说明**：为什么会发生？是不知道？忘了？还是流程没要求？
   - **改进意见**：如何避免再次发生？
   - **对流程的修改建议**：如果有，具体是什么？
6. 各 agent 的 SOUL.md 要求面对审计必须坦诚、实事求是：
   - 不否认事实（右倾）
   - 不无原则认下不属实的指责（左倾）
   - 不狡辩、不推诿、不欺骗

**main 汇总反馈：**
4. 确认 auditor 发现的问题
5. 追溯根因——问题是 MA 流程文档的缺陷，还是单次执行失误？
6. **如果是流程缺陷**（如缺少必做步骤、检查项不完整）→ 修改 skills/SKILL.md 或 projects/ma/<agent>/AGENTS.md
7. **如果是执行失误**（agent 跳步、遗漏）→ 记录到 RETROSPECTIVE.md，评估是否需要强化红线
8. 修改完成后同步 agent workspace
9. 写入 `projects/ma/RETROSPECTIVE.md`

#### 复盘产出

| 产出 | 内容 | 操作 |
|------|------|------|
| RETROSPECTIVE.md 更新 | 问题、根因、修复、教训 | main 写入 |
| SKILL.md / AGENTS.md 修改 | 流程文档补强 | main 执行 |
| agent workspace 同步 | 模板 → 实际 workspace | main 复制 |
| *(不改已完成项目)* | *(示例项目A 的 journey.md 保持原样)* | — |

#### 复盘红线

- ❌ 事后修改已完成项目的文档/代码来"美化"审计结果
- ❌ 用"改项目文件"替代"改流程文档"——治标不治本
- ❌ 复盘发现问题不写入 RETROSPECTIVE.md
- ❌ 混淆主客体——复盘是修组织流程，不是重修已完成的项目

#### 新增一轮开发 ≠ 复盘

如果 用户 要求在已完成项目上做改进（如补全 journey.md、修复 bug）：
- 这是一轮**新的团队研发任务**
- 走完整 0-10 阶段流程
- spec.md 写清楚"要改什么"
- 前置审计、编码、测试、终审一个不少
- 和复盘是完全独立的两件事

## 6. Agent 通信与调度

### 通信矩阵

```
        main    coder   tester  auditor publicist
main     -       ↔️       ↔️       ↔️       ↔️
coder    ↔️       -       ↔️       ↔️       —
tester   ↔️       ↔️       -       *↔️      —
auditor  ↔️       ↔️       *↔️      -       —
publicist↔️       —       —       —       —
```

- publicist 只与 main 通信
- auditor 发现问题 → 报告 main教练，不直接指挥 coder/tester
- tester↔auditor 标注 *↔️：审计提交走 main 转手（tester → main → auditor），测试过程可直通

### 调度方式

- **sessions_send** — 向 agent 发送任务，等待回复
- **docs/todo.md** — 共享任务看板，所有 agent 可读写
- **docs/journey.md** — 共享过程记录，所有 agent 写入关键节点
- **git** — 代码变更在 `projects/<project-name>/` 共享目录

### 状态跟踪

main 定期通过以下方式跟踪进度：
1. 查看 `docs/todo.md` — 各任务状态
2. 查看 `docs/journey.md` — 各 agent 写入的过程记录
3. 查看 git log — 提交历史

## 7. Git 工作流

```
main: git init → 初始化项目
    │
coder: 
    git checkout -b feature/xxx
    开发 → git add && git commit
    sessions_send → tester
    │
tester:
    git checkout feature/xxx
    测试 → 报告
    sessions_send → main（附测试报告，由 main 转交 auditor）
    或 sessions_send → coder（发现bug）
    │
auditor:
    git diff main...feature/xxx
    git log main..feature/xxx
    审计 → sessions_send → main
    │
main:
    git merge feature/xxx
    git branch -d feature/xxx
```

## 8. 进度通报机制 🔔

### 8.1 main（项目经理）的定时反馈

**频率：最少每 5 分钟一次。** main 必须在团队研发过程中持续向 用户 汇报进展。

**有进展时 — 标准简报：**
```
📊 [项目名] [复杂度S/M/L] [时间]
当前阶段：[阶段名] / 共[X]阶段
任务状态：
  - coder: [状态] [简要]
  - tester: [状态] [简要]
  - auditor: [状态] [简要]
  - publicist: [状态] [简要]
下一步：[行动]
```

> 阶段列表根据复杂度动态调整，不要求显示全部阶段。
> 例：S 级当前阶段 "3 coder: 实现 / 共6阶段"
> 例：M 级当前阶段 "5 main: analyze / 共9阶段"

**无进展时 — 一句话简报：**
```
📊 [项目名] [复杂度S/M/L] [时间] — [当前阶段]，[agent] 执行中，无异常。
```

**有风险/问题时 — 风险上报：**
```
⚠️ [项目名] [复杂度S/M/L] 风险提醒
风险：[描述风险是什么]
影响：[对进度/质量的量化影响]
已采取措施：[我们做了什么]
备选方案：[Plan B]
需要决策：[如果需要用户拍板，明确写出来]
```

### 8.2 agent 完成工作的通报

**每个 agent（coder / tester / auditor / publicist）完成自己的工作后：**

1. agent sessions_send → main（附带交付摘要）
2. **main 收到后，立即向 用户 发送通知**，内容包括：
   - 哪个 agent 完成了什么任务
   - 交付了什么（文件/报告/代码变更摘要）
   - 下一步谁接手、做什么

**禁止行为：**
- ❌ agent 完成任务后静默交接，用户 不知情
- ❌ main 攒多个 agent 的完成通知合并为一条（每个 agent 完成就立刻发）
- ❌ main 超过 5 分钟不向 用户 反馈（即使没有 agent 完成，也要发一句话简报）

## 9. 项目目录规范（遵循开源项目习惯，语言无关）

模板结构（具体项目按语言习惯裁剪）：

```
projects/<project-name>/
├── docs/                    ← 所有文档集中管理
│   ├── spec.md              ← main 产出，项目规格说明
│   ├── design.md            ← coder 产出，架构设计
│   ├── style-guide.md       ← main 产出，代码风格约定
│   ├── todo.md              ← main 创建维护，任务登记表
│   ├── test-report.md       ← tester 产出，测试报告
│   ├── audit-report.md      ← auditor 产出，审计报告
│   └── journey.md           ← 所有 agent 写入，过程记录
├── src/                     ← 源代码（按语言约定组织）
│   ├── main/java/...        ← Java (Maven/Gradle 约定)
│   ├── main.c, module.c...  ← C 项目平铺
│   └── package_name/...     ← Python 包
├── tests/                   ← 测试文件（Python/Go/JS）
│   └── (Java: 可选，Maven 项目测试放 src/test/)
├── include/                 ← C/C++ 头文件（可选，C项目需要）
├── scripts/                 ← 构建/运行/部署脚本
├── bin/                     ← 编译产物目录（.gitignore，不提交）
├── lib/                     ← 第三方依赖/库（可选）
├── README.md                ← publicist 产出（最后）
├── CHANGELOG.md             ← publicist/main 维护，版本变更记录
├── CONTRIBUTING.md          ← main 产出，贡献指南
├── Makefile / pom.xml / CMakeLists.txt / pyproject.toml
└── .gitignore               ← bin/ out/ *.class *.log .DS_Store
```

**按语言裁剪规则：**

| 语言 | src/ 下约定 | 测试放哪 | 构建文件 | bin/ lib/ |
|------|-----------|---------|---------|-----------|
| **Java** | `src/main/java/` + `src/main/resources/` | `src/test/java/` 或 `tests/` | pom.xml / build.gradle | bin/（编译产物，gitignore） |
| **C** | `src/*.c` 平铺或分子目录 | `tests/` | Makefile / CMakeLists.txt | bin/（可执行）+ lib/（.a/.so）+ include/（.h） |
| **Python** | `src/package_name/` 或项目根平铺 | `tests/` | pyproject.toml / setup.py | 不需要 bin/ lib/ |
| **Go** | 项目根平铺或 `cmd/` + `pkg/` | 同文件 `_test.go` | go.mod | 不需要 |
| **JS/TS** | `src/` | `tests/` 或 `__tests__/` | package.json | 不需要 |

**关键原则：**
- `docs/`、`README.md`、`CHANGELOG.md`、`CONTRIBUTING.md`、`.gitignore` 是所有项目标配
- `src/` 下的子目录按所选语言的社区惯例组织，coder 在架构设计时确定
- `bin/`、`lib/`、`include/` 按语言需要决定是否创建
- `tests/` 作为通用测试目录，Java 项目可用 `src/test/` 替代

## 10. 子Agent假设与依赖

本 skill 假设以下 agent 已在 OpenClaw 中配置（参考 `projects/ma/multi-agent-design.md` 第5节）：

- `coder` — workspace: `~/.openclaw/workspace-coder`，profile: coding
- `tester` — workspace: `~/.openclaw/workspace-tester`，profile: coding
- `auditor` — workspace: `~/.openclaw/workspace-auditor`，profile: coding
- `publicist` — workspace: `~/.openclaw/workspace-publicist`，profile: coding

每个 agent 的 SOUL.md 和 AGENTS.md 见 `projects/ma/<agent>/`。

**如果 agent 未配置：** main 应回退到 single-agent 模式，自行完成所有角色（按 AGENTS.md 规则执行 design→code→test→audit 全流程）。并在任务完成后告知 用户 "agent 未配置，由 main 单枪匹马完成"。

## 12. 异常处理

### 12.1 问题诊断流程（problem-solving-methodology）

**触发条件**：开发过程中出现任何需要排查的问题（auditor 发现问题、tester 发现 bug、coder 遇到技术障碍、迭代修复失败等），所有 agent 必须应用结构化诊断，禁止 guess-and-fix。

**核心流程**（详见 `skills/problem-solving-methodology/SKILL.md`）：

```
Step 0: 消解层 — 问题本身成立吗？（语言陷阱/假设检验/问题分类）
  ↓
Step 1: 定义问题 — 现象 vs 预期 vs 影响（不含假设）
  ↓
Step 2: 诊断 — 映射调用链 → 逐步验证 → 缩小范围 → 确认根因
         确认根因三问: Why?（机制解释）/ Sufficient?（充分性）/ Unique?（排他性）
  ↓
Step 3: 设计方案 — 至少2个候选方案，对比有效性/风险/复杂性/可逆性
  ↓
Step 4: 执行 — 一次只改一个变量，记录原始值
  ↓
Step 5: 验证 — 直接/回归/持久性三重验证
  ↓
Step 6: 复盘 — 根因一句话 + 弯路 + 教训
```

**防呆机制**：
- 同一假设连续3次验证无结论 → 换假设
- 累计诊断 >15分钟无进展 → 暂停，汇报已排除项
- 累计 >5个假设均被否定 → 考虑消解（回 Step 0）
- 修复后问题复现 → 不叠加补丁，回退重新诊断

**反模式（所有 agent 禁止）**：

| 反模式 | 表现 | 正确做法 |
|--------|------|---------|
| Guess-and-fix | 看到现象 → 直接改 | 先映射调用链 |
| Surface fix | 改错误值，不问为什么是这个值 | 追问"为什么会变成这个值？" |
| Multi-change | 一次改3个地方 | 一次一个变量 |
| Premature victory | "应该好了"没有证据 | 展示验证结果 |
| No rollback | 忘记记录原始值 | 改前备份 |

### 12.2 标准异常处理

| 异常 | 处理 |
|------|------|
| auditor 无响应超过 30min | main 重新触发审计或告知 用户 |
| coder/tester 卡住 | main 主动 sessions_send 询问状态 |
| agent 模型报错/超时 | main 重试一次，仍失败则告知 用户 |
| 需求在开发中变更 | **走完整需求变更流程（见下）** |
| 审计不通过反复迭代 | 3轮后 main 向 用户 汇报情况，请求决策 |

### 需求变更处理流程 🔴

**核心原则：最大继承，追加修改，不覆盖。**

```
已有产出物（spec.md / 代码 / 测试）不删除、不重写。
变更是增量——在原有基础上追加和修改，保留完整的演进历史。
```

#### 变更流程

```
用户 提出变更
    │
    ▼
1 main 追加写入 spec.md（新增"需求变更记录"章节，不覆盖原需求）
    │
    ▼
2 auditor delta 前置审计（只审变更部分，与原需求对比，检查冲突）
    │
    ▼
3 main 判断影响范围
    ├── 影响已有代码 → 走变更开发流程（456）
    └── 不影响已有代码 → 按原流程继续
    │
    ▼
4 coder 追加修改（不重写已有代码，追加新功能或修改受影响部分）
    │
    ▼
5 tester delta 测试（保留所有原测试用例，追加变更相关用例）
    │
    ▼
6 auditor delta 终审（对照变更基线追溯，确认无回归破坏）
    │
    ▼
7 main merge → publicist 补充文档 → main 审阅 → 通知 用户
```

#### spec.md 变更记录格式（追加，不覆盖）

```markdown
---

## 需求变更记录

### 变更 #1 — YYYY-MM-DD HH:MM
**变更人：** 用户
**变更摘要：** [一句话描述]
**变更内容：**
- 原需求：[引述原需求相关段落]
- 新需求：[描述变更后]
**影响范围：**
- 代码：[已有代码中哪些部分受影响]
- 测试：[哪些测试用例需要调整或新增]
- 文档：[README/设计文档的哪些章节需要更新]
**关联：** 见 audit-report.md 第X次变更审计
```

#### 各角色在需求变更中的要求

| 角色 | 变更中的职责 | 禁止行为 |
|------|------------|---------|
| **main** | 追加写入 spec.md（不重写原需求）；判断影响范围；通知所有相关 agent | ❌ 删除或重写原需求 ❌ 跳过 delta 审计 |
| **auditor** | delta 审计——只审变更部分；检查与原需求的冲突和矛盾；确认无回归破坏 | ❌ 等同于全新审计重新来一遍 ❌ 忽略变更与原需求的继承关系 |
| **coder** | 在已有代码上追加修改；git checkout 不删除已有功能；design.md 追加变更说明 | ❌ 重写整个文件 ❌ 删除原有功能除非明确要求 |
| **tester** | 保留所有原测试用例；追加变更相关测试用例；delta 回归确保无破坏 | ❌ 删除原有测试用例 ❌ 只测新功能不测回归 |
| **publicist** | README 追加变更说明；summary.md 记录变更过程 | ❌ 重写整个文档 |

#### 变更记录到 journey.md

```markdown
| YYYY-MM-DD HH:MM | 需求变更 #1 | 用户 提出变更：[摘要]。影响：[范围判断]。进入变更流程 |
| YYYY-MM-DD HH:MM | 变更审计完成 | auditor delta 审计通过。变更不影响已有代码/影响X处 |
```

#### 变更示例：追加 vs 覆盖

| 操作 | ❌ 覆盖（禁止） | ✅ 追加修改（正确） |
|------|-------------|-----------------|
| spec.md | 把原需求"兔子跳过狐狸"改为"兔子跳过刺猬" | 原需求保留；新增变更记录 #1：角色从狐狸改为刺猬 |
| 代码 | 删除狐狸的 CSS，重写刺猬的 CSS | 新增 `.hedgehog` 的 CSS；`.fox` 改为 `display:none`（保留原代码） |
| 测试 | 删除狐狸测试用例，重新写一套 | 原测试用例保留（标注为"原需求"）；新增变更测试用例组 |
| README | 全文替换 | 项目概述保留；新增"需求变更记录"小节 |
| design.md | 覆盖架构设计 | 原方案保留；新增"## 变更 #1 架构调整"小节 |

## 13. 红线

- **走合规流程** — 按复杂度 S/M/L 执行裁剪后流程，不允许跳过规则内的必要步骤
  - [L级] auditor 前置审计 + 交付终审，不允许跳过
  - [M级] auditor 前置审计 + 交付终审（可合并或轻量）
  - [S级] main 自查替代独立 auditor 前置审计，但终审仍需 auditor
- **只有 auditor 通过后才汇报完成** — 测试通过 ≠ 可交付
- **main 不越俎代庖** — 编码让 coder 做，测试让 tester 做，审计让 auditor 做
- **所有产出写入 projects/** — Git 管理，不丢失
- **失败必须说明原因** — 任何 agent 出问题，向 用户 说明原因和当前状态
- **🔴 每个角色完成工作必须通知 用户** — agent 完成→通知 main → main 立即通知 用户。禁止静默交接。
- **🔴 main 最少每 5 分钟向 用户 反馈进展** — 即使无变化也要发状态更新。不让 用户 等。
- 🔴 **todo.md 和 journey.md 是项目宪法** — 贯穿全部12个阶段。修改、删除、削弱均须 用户 最高层同意。任何人（含main）不得擅自操作。
- 🔴 **阶段结束标志 = 该阶段 todo 全部 ✅** — 不凭感觉，不看时间，只看 todo。
- 🔴 **编译产物交付门禁** — 需要编译的项目，main 不得在 tester+coder 迭代完成、测试全通过、auditor 终审前将 bin/apk/jar 等编译产物交付给 用户。
- 🔴 **coder-tester 直接交互必须留痕** — 阶段7中 coder 和 tester 可直接交互提高效率，但每轮必须更新 todo.md 和 journey.md，首次 bug 和最终通过必须告知 main。迭代上限 3 轮。
- 🔴 **coder 实现不确定必须升级** — 阶段4/6 发现 examples 无参考、实现方案不确定时，coder 不擅自决定。升级路径：coder→main→tester（可测试性评估）→main→coder，决策依据记录到对应阶段的 design.md（阶段4）或 journey.md（阶段6）。
