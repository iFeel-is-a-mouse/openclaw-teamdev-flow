# MA 团队研发框架 — 变更日志

## [3.1.0] — 2026-05-30

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

- `skills/team-dev/SKILL.md` — §4.0.5、§5 阶段4/6/7/9、§13 红线、D8 决策节点
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
