# MA 团队研发框架 — 变更日志

## [3.5.0] — 2026-05-31

### 文档去重与一致性（v3.4.0 复盘遗留项）

- **核心文档去重** — SKILL.md 移除重复的角色表和通信矩阵（链接到 multi-agent-design.md）
- **配置格式统一** — multi-agent-design.md 的 JSON5 配置示例改为 YAML，与 SETUP.md 一致
- **术语统一** — 全局 `session_send`→`sessions_send`，`spec kit`→`spec-kit`

### 新增板块

- **SETUP.md 5分钟速配** — 文件开头增加 TL;DR 板块，4 条命令 + 1 段话
- **git pull 冲突处理指南** — SETUP.md "后续更新" 增加冲突处理 5 步流程
- **main 决策留痕格式** — journey-template.md 新增决策留痕标准格式
- **README 验证步骤** — 快速开始增加"验证配置"步骤

### 影响文件

- README.md, SETUP.md, multi-agent-design.md
- skills/SKILL.md
- main/journey-template.md

## [3.4.0] — 2026-05-31

### 复盘修复（4 agent 联合审查）

- **阶段计数统一** — 全文统一为 "12 阶段（0-11，含按需复盘）"，修复多处 "11 阶段" 不一致
- **README 工作流图补全** — 增加阶段10(publicist定稿)和阶段11(复盘)，增加核心链路摘要
- **版本号同步** — SKILL.md 版本从 3.1.0 同步到 3.4.0
- **路径修复** — `skills/team-dev/SKILL.md` → `skills/SKILL.md`，消除引用不一致
- **新增模板** — `auditor/templates.md`、`tester/templates.md`
- **文档导航补全** — README 导航树增加 CHANGELOG.md、RETROSPECTIVE.md、VERSION、skills/SKILL.md

### 影响文件

- README.md, SETUP.md, CHANGELOG.md, VERSION
- multi-agent-design.md, sequence-diagram.md
- skills/SKILL.md
- main/AGENTS.md
- auditor/AGENTS.md, auditor/templates.md
- tester/AGENTS.md, tester/templates.md

## [3.3.0] — 2026-05-31

### 开源准备

- **脱敏** — 移除个人信息和历史项目代号，GitHub 仓库公开
- **快速开始** — 新增 4 步快速开始指南（下载→创建Agent→融合初始化→团队研发）
- **仓库信息固化** — SETUP.md 固化 GitHub 仓库地址
- **融合机制** — SETUP.md 新增"初始化：融合行为准则到各 Agent Workspace"章节，强调 MA:CORE 标记机制、融合非覆盖

### 模板体系建立

- **新增 4 个模板** — `main/todo-template.md`（WBS+断点再续）、`main/journey-template.md`（todo镜像+闭环子模式）、`auditor/templates.md`（登记→跟踪→验证→闭环）、`tester/templates.md`（登记→修复→验证→关闭）
- **批判继承** — 从 sequence-diagram.md 提取 WBS 逐步求精、建任务权限矩阵、断点再续，融合到 todo-template
- **SKILL.md 引用** — 各模板在 SKILL.md 中建立引用关系

### 架构文档精简

- **multi-agent-design.md 重写** — 914行→286行（-69%），去重角色定义和工作流描述，提炼为纯架构文档
- **sequence-diagram.md 精简** — 移除重复的模板内容，保留时序图+引用
- **全 agent 知识对齐** — 5 个 AGENTS.md + 5 个 SOUL.md 均补充模板引用和架构文档知识

### README/SETUP 增强

- README 和 SETUP 的文件索引、学习路径中补充 multi-agent-design.md 和 sequence-diagram.md 的定位描述

### 影响文件

- README.md, SETUP.md, multi-agent-design.md, sequence-diagram.md
- skills/SKILL.md
- main/AGENTS.md, main/SOUL.md, main/todo-template.md, main/journey-template.md
- coder/AGENTS.md, coder/SOUL.md
- tester/AGENTS.md, tester/SOUL.md, tester/templates.md
- auditor/AGENTS.md, auditor/SOUL.md, auditor/templates.md
- publicist/AGENTS.md, publicist/SOUL.md

---

## [3.2.0] — 2026-05-30

### 新增
- 项目版本管理与 Git 隔离规范（§4.5）：SemVer 版本号 + VERSION/CHANGELOG.md/.gitignore
- 简化的 Git 工作流：大版本合并 master，小版本一条分支，hotfix 同分支串行
- MA 框架独立仓库（projects/ma/），与项目仓库 git 隔离

### 变更
- §4.1 中的 git init 和 .gitignore 合并到 §4.5.1，消除重复
- 编号修正：§4.4 项目 Git 与版本管理 → §4.5（原 §4.4 保留为"前置检查：Projects 软链接"）
- 阶段9 版本操作简化

- **coder-tester 直接交互** — 阶段7测试发现 bug 时，coder 和 tester 可直接交互修复，不经过 main。约束：每轮更新 todo.md/journey.md，首尾告知 main，迭代上限 3 轮，超限由 main 介入裁决。

### 监督增强

- **main 确认回执机制** — 阶段7每次迭代的 todo.md 记录需 main 标记确认
- **迭代计数规则** — 明确定义一轮迭代的边界（提交修复→验证→记录），同一修复引入的新 bug 不计新轮次
- **超限冻结策略** — tester 继续测试其他用例，暂停争议 bug 的反复验证
- **审计增强** — auditor 新增 4 项流程合规检查（升级路径、直接交互留痕、main 确认、产物交付前置）

### 文档同步

- 三处记录位置措辞统一：阶段4→design.md，阶段6→journey.md
- D6 前置条件补齐（tester+coder 迭代完成 + 测试通过 + auditor 终审）
- D6.5 扩展为正常规则 + 超限处理
- 全部 agent AGENTS.md 同步更新（coder/tester/auditor/main）

### 影响文件

- `skills/SKILL.md` — §4.0.5、§5 阶段4/6/7/9、§13 红线、D8 决策节点
- `AGENTS.md`（workspace）— M级流程说明、§6-9 实现到交付、§7 异常处理
- `projects/ma/main/AGENTS.md` — 同步
- `projects/ma/main/SOUL.md` — D6/D6.5/D8 决策节点
- `projects/ma/coder/AGENTS.md` — 沟通规范、红线
- `projects/ma/tester/AGENTS.md` — 工作流程、沟通规范、红线
- `projects/ma/auditor/AGENTS.md` — 流程合规检查、交付终审 checklist、红线

---

## [3.0.0] — 2026-05-25

- todo.md + journey.md 确立为项目宪法核心
- 阶段结束标志 = 该阶段 todo 全部 ✅
- todo.md/journey.md 修改/删除需 用户最高层同意

## [2.2.0] — 2026-05-24

- todo.md + journey.md + checklist.md 三件套关系澄清
- 用户决策落地
- 四方审阅修订

## [2.1.0] — 早期版本

- 初始 MA 框架建立
- 5 角色模型（main/coder/tester/auditor/publicist）
- S/M/L 复杂度分级
- spec-kit 集成（constitution/clarify/analyze/checklist）

## [2.0.0] — 更早版本

- MA 架构设计完成
- 团队研发触发机制
- 全流程 11 阶段工作流
