# MA 团队研发框架 — 变更日志

## [4.3.0] — 2026-06-08

### 全量一致性审查 + 路由规则 + JSON 结构化

- **一致性问题修复**（基于三路 agent 联合审查）：
  - codereviewer AGENTS.md 新增阶段4b设计审查流程（原缺失，P0）
  - auditor AGENTS.md 新增复盘审计职责 + 变更质量门禁（P1）
  - tester AGENTS.md 新增 delta回归/非阻塞追踪 2条红线（P1）
  - SKILL.md metadata version 同步修复（P1反复问题，登记到 recurring-issues）
  - 创建 docs/recurring-issues.md 登记版本号不同步（P1）
- **路由规则**（各 agent 缺少 MA 框架认知）：
  - SKILL.md YAML 新增路由表（agents/routing/routing-redirects）
  - 全部5个 agent AGENTS.md 新增 ROUTING 标记 + 转发规则
  - 修复：用户直接 message coder/tester 说"团队研发"时不转给 main 的问题
- **格式优化**：
  - §2 生命周期表 → JSON + Mermaid 时序图（LLM 可精确解析）
  - §4 自查清单表 → JSON
  - §10.4 质量门禁表 → JSON
  - §11.1 原则表 → JSON
  - 全部 ASCII 决策树/流程图 → 结构化表格或 JSON
- **README.md 保持纯表格格式**（给人看的文档）
- **VERSION 统一为 4.3.0**

### 影响文件

- `projects/ma/VERSION` — 4.2.0 → 4.3.0
- `projects/ma/docs/change-management.md` — JSON 嵌入 + Mermaid
- `projects/ma/skills/SKILL.md` — 路由表 YAML
- `projects/ma/coder/AGENTS.md` — 路由规则
- `projects/ma/tester/AGENTS.md` — 路由规则 + 红线
- `projects/ma/codereviewer/AGENTS.md` — 路由规则 + 阶段4b
- `projects/ma/auditor/AGENTS.md` — 路由规则 + 复盘审计
- `projects/ma/publicist/AGENTS.md` — 路由规则
- `projects/ma/CHANGELOG.md` — 本条目

## [4.2.0] — 2026-06-08

### 变更管理强化

- **新增变更管理规范** — `docs/change-management.md`，覆盖：
  - 变更生命周期（CR登记→影响分析→设计审查→实现→测试→终审→闭合）
  - CR 模板（完整版 + S 级简化版）
  - 影响分析 10 项自查清单
  - 核心文件（>500行）修改规则 — main 不得直接 edit
  - 跨模块数据变换设计规范 — design.md 必须给出≥2组输入→输出示例
  - 非阻塞问题追踪规则 — 必须记入 todo.md，不得口头约定
  - Tester 模型冗余策略（primary→fallback 自动降级）
  - 角色互换常态化策略（每3个Sprint一次）
- **新增自我改进机制（基于《原则》Ray Dalio）** — change-management.md §11：
  - 疼痛+反思=进步闭环
  - 不容忍两次同样的错误 — recurring-issues.md 重复性问题追踪
  - 五步复盘法（目标→问题→诊断→方案→执行）
  - 流程健康度巡检（季度指标）
  - 改进思维 vs 问责思维
- **SKILL.md 结构重构（对标 Skill 编写规范）** — 见 kb/AI开发/Skill编写/2026-06-05-如何写好Skill.md：
  - 添加 YAML 元数据头（name/description/trigger-keywords）
  - 新增"做什么/不做什么/什么时候用"概述
  - 新增环境前置检查步骤和命令
  - 新增执行流程决策树速览
  - 新增阶段间检查点表和超限处理流程
  - 新增常见问题与故障排查（FAQ §12.5）
- **SKILL.md 阶段11 复盘增强** — 融入五步流程法和三层根因诊断
- **SKILL.md §11 新增变更管理章节** — 8 条核心规则 + 变更流程整合图
- **SKILL.md §13 新增 6 条变更管理红线**
- **main/AGENTS.md 新增变更管理关联文档和启动时必读**
- **coder/AGENTS.md 新增 3 条变更管理红线**
- **codereviewer/AGENTS.md 新增变更管理项目知识**
- **README.md 文档导航更新** — 新增 change-management.md 入口

### 多角色审查后修复（codereviewer/coder/auditor 联合审查）

- **codereviewer/AGENTS.md 新增阶段4b设计审查流程**（P0审计项修复）
- **auditor/AGENTS.md 新增复盘审计职责 + 变更质量门禁checklist**（P1审计项修复）
- **tester/AGENTS.md 新增 2 条变更管理红线**（delta回归 + 非阻塞追踪）
- **创建 docs/recurring-issues.md**（P1 — 版本号不同步重复问题登记）
- **change-management.md §6.3 简单映射豁免条件**（P1 — 直接赋值可不给2组示例）
- **change-management.md §5.2 澄清 Edit/write 工具范围**（P1 — write 也不适用核心文件）
- **change-management.md §10.4 门禁表增加确认人归属**（P1）
- **SKILL.md metadata version 同步修复**（P1 — 3.6.0→4.2.0，登记到 recurring-issues.md）

### 影响文件

- `projects/ma/docs/change-management.md`（新文件）
- `projects/ma/docs/recurring-issues.md`（新文件）
- `projects/ma/skills/SKILL.md` — §11 变更管理、§13 红线、metadata版本
- `projects/ma/main/AGENTS.md` — 关联文档 + 启动加载列表
- `projects/ma/coder/AGENTS.md` — 红线
- `projects/ma/codereviewer/AGENTS.md` — 阶段4b设计审查流程、项目知识
- `projects/ma/tester/AGENTS.md` — 红线
- `projects/ma/auditor/AGENTS.md` — 复盘审计职责、变更质量门禁
- `projects/ma/README.md` — 文档导航
- `projects/ma/CHANGELOG.md` — 本条目
- `projects/ma/VERSION` — 4.1 → 4.2

## [3.9.0] — 2026-06-04

### 复盘决议实施

- **模型分配最终确定** — coder=deepseek-v4-pro, tester=moonshot/kimi-k2.6, codereviewer=zai/glm-5.1
- **性能测试分工** — tester 执行, auditor 定标准, coder 定指标
- **安全弱化** — codereviewer 基础扫描即可，不设专项审计
- **文档变更红线** — 禁止 `cp` 覆盖，必须 edit 融合+备份
- **README 补全** — 团队表和工作流加入 codereviewer+结对编程

## [3.8.0] — 2026-06-03

### 结对编程工作流

- **新增 codereviewer 角色** — 代码审查员 & 导航员，使用 zai/glm-5.1
- **结对编程模式** — coder ⟷ codereviewer 轮换驾驶员/导航员
- **审查输出规范** — 🔴🟡🟢 三级标记
- **文档变更流程** — §8 新增，只能修改不能覆盖

## [3.7.0] — 2026-06-03

### 模型切换

- **主力模型恢复** — deepseek/deepseek-v4-pro 恢复为主力
- **codereviewer 互补模型** — 保持 zai/glm-5.1 不变
- **多模型架构定稿** — 不同角色使用不同模型互补

## [3.6.0] — 2026-06-03

### codereviewer 角色加入

- **架构扩展** — codereviewer 加入 MA 团队，位于 coder→tester 之间
- **multi-agent-design.md** — 架构图更新、角色定义、通信矩阵
- **各 agent AGENTS.md** — 同步更新协作关系

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
- **新增模板** — `auditor/audit-report-template.md`、`tester/test-report-template.md`
- **文档导航补全** — README 导航树增加 CHANGELOG.md、RETROSPECTIVE.md、VERSION、skills/SKILL.md

### 影响文件

- README.md, SETUP.md, CHANGELOG.md, VERSION
- multi-agent-design.md, sequence-diagram.md
- skills/SKILL.md
- main/AGENTS.md
- auditor/AGENTS.md, auditor/audit-report-template.md
- tester/AGENTS.md, tester/test-report-template.md

## [3.3.0] — 2026-05-31

### 开源准备

- **脱敏** — 移除个人信息和历史项目代号，GitHub 仓库公开
- **快速开始** — 新增 4 步快速开始指南（下载→创建Agent→融合初始化→团队研发）
- **仓库信息固化** — SETUP.md 固化 GitHub 仓库地址
- **融合机制** — SETUP.md 新增"初始化：融合行为准则到各 Agent Workspace"章节，强调 MA:CORE 标记机制、融合非覆盖

### 模板体系建立

- **新增 4 个模板** — `main/todo-template.md`（WBS+断点再续）、`main/journey-template.md`（todo镜像+闭环子模式）、`auditor/audit-report-template.md`（登记→跟踪→验证→闭环）、`tester/test-report-template.md`（登记→修复→验证→关闭）
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
- tester/AGENTS.md, tester/SOUL.md, tester/test-report-template.md
- auditor/AGENTS.md, auditor/SOUL.md, auditor/audit-report-template.md
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
