# journey-template.md — 项目过程日志模板

> 复制此模板到 `projects/<项目名>/docs/journey.md`。
> **todo.md 的镜像——每完成一行 todo，同时在 journey 追加一条记录。**
> 审计和复盘的关键依据。5 个 agent 均可写入。修改/删除/削弱需用户最高层同意。

---

## 第 1 条：流程声明

```markdown
## [YYYY-MM-DD HH:mm] 阶段0 完成：复杂度评估与流程声明

📋 复杂度评估: [S/M/L]（依据：[简述]）
📋 执行流程: [列出将执行的阶段]
📋 跳过/合并: [列出跳过的阶段及原因]
📋 宪章核心原则: [3-5条可验证原则]（L 级）

用户确认：✅
```

---

## 日常记录格式（镜像 todo.md）

每完成 todo.md 中一行任务，追加一条记录：

```markdown
## YYYY-MM-DD

### HH:mm [agent] 阶段N [阶段名] 启动
- todo #N 🔄

### HH:mm [agent] 阶段N [阶段名] 完成
- todo #N ✅
- 关键产出: [交付物列表]
- 关键决策: [决策及依据]
```

**完整示例：**

```markdown
## 2026-05-24

### 14:30 [main] 阶段0 复杂度评估启动
- todo #0 🔄
- 分析需求，评估为 M 级（200-1000行、4-10文件、2-3模块）

### 14:35 [main] 阶段0 完成
- todo #0 ✅
- 流程声明：0→1→2→3→4→5→6→7→8→9（9阶段）
- 跳过：constitution.md（M级按需）、publicist独立阶段（并入#9）

### 14:40 [main] 阶段1 需求分析启动
- todo #1 🔄

### 15:20 [main] 阶段1 完成
- todo #1 ✅
- 产出: docs/spec.md, docs/style-guide.md

### 15:22 [main] 阶段2 前置审计启动
- todo #2 🔄
- sessions_send → auditor: 前置审计

### 15:30 [auditor] 阶段2 完成
- todo #2 ✅
- clarify 发现 3 项歧义，详见 audit-report.md
- 宪章一致性：通过

### 15:32 [main] 阶段3 把关完成
- todo #3 ✅
- 审查审计报告，质量合格，委派 coder

### 15:35 [coder] 阶段4 架构设计启动
- todo #4 🔄
- 分支: feature/user-auth

### 16:10 [coder] 阶段4 完成
- todo #4 ✅
- 产出: docs/design.md + 可测试项清单（12项）
- 关键决策: 使用 JWT + Redis 方案，放弃 Session 方案（无状态需求）
- 交付 main

### 16:12 [main] 阶段5 analyze 完成
- todo #5 ✅
- spec ↔ design ↔ constitution 一致性检查通过

### 16:15 [coder] 阶段6 编码启动
- todo #6 🔄

### 17:00 [coder] 阶段6 完成
- todo #6 ✅
- 代码文件: UserAuth.java, AuthService.java, AuthController.java
- commit: a1b2c3d
- sessions_send → tester: 代码就绪，分支 feature/user-auth

### 17:05 [tester] 阶段7 测试启动
- todo #7 🔄
- 冒烟通过，用例设计完毕（黑盒8+白盒4=12项）

### 17:20 [tester] Bug 发现 - 迭代第1轮
- BUG-001: 密码为空时 NPE，级别 P1
- sessions_send → coder: BUG-001 详情

### 17:25 [coder] BUG-001 修复
- 根因: AuthService.validate() 未做 null 检查
- 修复: 添加 @NonNull 校验，commit b2c3d4e
- sessions_send → tester: 已修复

### 17:30 [tester] BUG-001 验证通过
- 原用例复测 ✅
- 回归测试 4 项 ✅ 无新增缺陷
- 迭代第1轮闭环

### 17:35 [tester] 阶段7 完成
- todo #7 ✅
- 测试结果: 用例12/通过12/bug1(已修复)，覆盖率85%
- 写入 docs/test-report.md
- sessions_send → main: 测试通过，附报告

### 17:38 [main] 阶段8 终审启动
- todo #8 🔄
- sessions_spawn → auditor: 交付终审

### 17:50 [auditor] 阶段8 完成
- todo #8 ✅
- 产出: docs/checklist.md (16/18通过, 2 NA)
- 产出: docs/audit-issues.md (AUDIT-001 发现→已修复→验证通过→闭环)
- 前置基线追溯: 全部覆盖
- 流程合规: coder-tester直接交互留痕完整 ✅
- 终审结论: 通过，准予交付

### 17:52 [main] 阶段9 merge+README
- todo #9 ✅
- git merge feature/user-auth → main
- README.md 初稿完成
- 交付用户
```

---

## 审计闭环记录（子模式）

审计发现问题时，在 journey.md 中追加闭环记录：

```markdown
### HH:mm [auditor] 审计问题登记 AUDIT-001
- 级别: P0/P1/P2/P3 | 分类: 完整性/安全性/可测性/宪章一致性/歧义
- 问题: [描述] | 基线对照: [引用 spec/constitution]

### HH:mm [coder] AUDIT-001 修复
- 根因: [分析] | 方案: [描述] | 影响文件: [列表]

### HH:mm [tester] AUDIT-001 验证
- 结果: ✅/❌ | 回归范围: [用例] | 回归结果: ✅/⚠️

### HH:mm [auditor] AUDIT-001 闭环
- 终审裁决: ✅放行/🔁继续修复/📝技术债务 | 理由: [依据]
```

详见问题管理模板 `projects/ma/auditor/templates.md`。

---

## 测试 Bug 闭环记录（子模式）

```markdown
### HH:mm [tester] Bug 登记 BUG-001 (迭代第N轮)
- 级别: P0/P1/P2/P3 | 模块: [名称]
- 现象: [描述] | 复现: [步骤] | 期望 vs 实际: [对比]

### HH:mm [coder] BUG-001 修复
- 根因: [分析] | 方案: [描述] | commit: [hash]

### HH:mm [tester] BUG-001 验证
- 原用例 ✅/❌ | 回归: [N项] ✅/⚠️ | 结论: 关闭/重开
```

详见缺陷模板 `projects/ma/tester/templates.md`。

---

## 迭代追踪表

| 轮次 | 触发 | 修复人 | 验证人 | 结果 | todo.md | journey.md |
|------|------|--------|--------|------|---------|-----------|
| 1 | BUG-001 NPE | coder | tester | ✅ | 子任务追加 | 17:20-17:30 |
| 2 | | | | | | |
| 3 | | | | | | |

> 迭代上限 3 轮。超限 main 介入裁决。

---

## main 决策留痕格式

main 在以下场景做出决策时，必须在 journey.md 追加记录：

```markdown
### HH:mm [main] 决策 [编号]
- 触发条件: [审计不通过/迭代超限/需求变更/意见分歧/宪法冲突]
- 决策内容: [接受/修复/技术债务/变更需求/修改宪章]
- 决策依据: [理由，引用 spec/constitution 条目]
- 影响范围: [影响的阶段/agent/文件]
- 执行人: [谁执行这个决策]
```

**决策触发条件：**
1. auditor 终审 checklist 未 100% 通过
2. coder-tester 迭代超过 3 轮
3. auditor 与 tester 意见分歧
4. 需求变更涉及宪法原则
5. constitution 与需求冲突
