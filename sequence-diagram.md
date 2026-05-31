# MA v2.2 时序图 — 以 todo.md 和 journey.md 为核心

## 核心设计

```
todo.md   = 项目大脑 — WBS任务看板，5个agent透明可编辑
           阶段结束标志 = 该阶段所有todo完成
           断点再续依据 = 最后一个完成的todo是谁、下一个待开始的todo是谁

journey.md = 项目日志 — 任务下达和完成时真实记录
            审计依据 = 对照todo看流程是否完整
            复盘依据 = 还原项目全过程
```

## UML 时序图

```plantuml
@startuml
title MA v2.2 — 12阶段全流程时序图\n(todo.md和journey.md贯穿全程)

actor 用户
participant "main\n(主控)" as Main
participant "auditor\n(审计)" as Auditor
participant "coder\n(编码)" as Coder
participant "tester\n(测试)" as Tester
participant "publicist\n(写手)" as Publicist

database "todo.md\n(任务看板)" as Todo
database "journey.md\n(过程日志)" as Journey
collections "docs/\n(项目文档)" as Docs

== 阶段0：宪章 ==

用户 -> Main : 团队研发: [需求]
activate Main

Main -> Todo : 写入 #0 制定宪章 🔄
Main -> Journey : 记录: 阶段0启动
Main -> Docs : 读取 constitution-template.md
Main -> Docs : 写入 constitution.md
Main -> Todo : #0 ✅ 完成
Main -> Journey : 记录: 阶段0完成，宪章核心原则: [摘要]

== 阶段1：需求分析 ==

Main -> Todo : 写入 #1 需求分析 🔄
Main -> Journey : 记录: 阶段1启动
Main -> Docs : 写入 spec.md + style-guide.md + CONTRIBUTING.md
Main -> Todo : #1 ✅ 完成
Main -> Journey : 记录: 阶段1完成

== 阶段2-3：前置审计+clarify + main把关 ==

Main -> Todo : 写入 #2 前置审计 🔄
Main -> Journey : 记录: 阶段2启动
Main -> Auditor : sessions_send: 前置审计
activate Auditor
Auditor -> Docs : 读取 constitution + spec + style-guide
Auditor -> Docs : 写入 audit-report.md (前置基线+clarify)
Auditor -> Journey : 记录: 阶段2完成，clarify发现N项
Auditor -> Todo : #2 ✅ 完成
Auditor --> Main : 审计报告
deactivate Auditor

Main -> Todo : 写入 #3 把关 🔄
Main -> Journey : 记录: 阶段3启动
Main -> Docs : 审查audit-report.md
Main -> Todo : #3 ✅ 通过，委派coder
Main -> Journey : 记录: 阶段3完成，决策: 通过

== 阶段4-5：架构设计 + analyze ==

Main -> Todo : 写入 #4 架构设计 🔄
Main -> Journey : 记录: 阶段4启动
Main -> Coder : sessions_send: 架构设计+可测试项清单
activate Coder
Coder -> Docs : 读取 constitution + spec + style-guide
Coder -> Docs : 写入 design.md + 可测试项清单
Coder -> Journey : 记录: 阶段4完成，关键设计决策
Coder -> Todo : #4 ✅ 完成
Coder --> Main : design.md + 可测试项清单
deactivate Coder

Main -> Todo : 写入 #5 analyze 🔄
Main -> Journey : 记录: 阶段5启动
Main -> Docs : 读取 constitution + spec + design
Main -> Docs : 交叉检查 spec ↔ design ↔ constitution
Main -> Todo : #5 ✅ 通过
Main -> Journey : 记录: 阶段5完成，分析通过

== 阶段6：编码实现 ==

Main -> Coder : analyze通过，开始编码
Main -> Todo : 写入 #6 编码 🔄
Main -> Journey : 记录: 阶段6启动
activate Coder
Coder -> Docs : 编码写入 src/
Coder -> Docs : git commit & push feature分支
Coder -> Journey : 记录: 阶段6完成，代码文件列表
Coder -> Todo : #6 ✅ 完成
Coder --> Tester : sessions_send: 代码就绪
Coder --> Main : 交付摘要
deactivate Coder

== 阶段7：测试 ==

Main -> Todo : 写入 #7 测试 🔄
Main -> Journey : 记录: 阶段7启动
activate Tester
Tester -> Docs : 读取 constitution + spec + design + 可测试项清单
Tester -> Docs : git checkout feature分支
Tester -> Docs : 写入 test-report.md
Tester -> Journey : 记录: 阶段7完成，用例N/通过N/bugN
Tester -> Todo : #7 ✅ 完成
Tester --> Main : 测试报告
deactivate Tester

== 阶段8：交付终审+checklist ==

Main -> Todo : 写入 #8 终审 🔄
Main -> Journey : 记录: 阶段8启动
Main -> Auditor : sessions_spawn: 交付终审
activate Auditor
Auditor -> Docs : 读取全部项目文档 + git diff
Auditor -> Docs : 生成 checklist.md (质量验证清单，不同于todo)
Auditor -> Docs : 写入 audit-report.md (终审，对照前置基线)
Auditor -> Docs : checklist.md 逐项打勾
Auditor -> Journey : 记录: 阶段8完成，checklist N/N通过
Auditor -> Todo : #8 ✅ 通过
Auditor --> Main : 终审通过 + checklist状态
deactivate Auditor

== 阶段9：merge + README初稿 ==

Main -> Todo : 写入 #9 merge+初稿 🔄
Main -> Journey : 记录: 阶段9启动
Main -> Docs : git merge feature分支
Main -> Docs : 写入 README.md 初稿
Main -> Todo : #9 ✅ 完成
Main -> Journey : 记录: 阶段9完成

== 阶段10：publicist总结完善 + main定稿 ==

Main -> Todo : 写入 #10 文档定稿 🔄 (publicist+main)
Main -> Journey : 记录: 阶段10启动
Main -> Publicist : sessions_send: README初稿 + 项目上下文
activate Publicist
Publicist -> Docs : 读取 constitution + checklist + 代码 + journey + README初稿
Publicist -> Docs : 完善 README.md 定稿
Publicist -> Docs : 写入 docs/summary.md
Publicist -> Journey : 记录: 阶段10 publicist完成
Publicist --> Main : README定稿 + summary
deactivate Publicist

Main -> Docs : 审阅 README定稿 (必查6项)
Main -> Todo : #10 ✅ 完成
Main -> Journey : 记录: 阶段10完成，交付用户
Main --> 用户 : 项目完成，文档已就绪
deactivate Main

== 阶段11：事后复盘（按需） ==

用户 -> Main : 复盘
activate Main
Main -> Todo : 写入 #11 复盘 🔄
Main -> Journey : 记录: 阶段11启动
Main -> Auditor : sessions_spawn: 复盘审计
activate Auditor
Auditor -> Docs : 读取 journey.md + todo.md + docs/*
Auditor --> Main : 过程记录审计报告
deactivate Auditor
Main -> Docs : 修改MA流程文档
Main -> Docs : 更新 RETROSPECTIVE.md
Main -> Todo : #11 ✅ 完成
Main -> Journey : 记录: 阶段11完成
deactivate Main

@enduml
```

## todo.md vs checklist.md 分工（修正）

| | todo.md | checklist.md |
|------|---------|-------------|
| **本质** | 过程管理（做什么事） | 结果验证（做对了没有） |
| **谁写** | 5个agent都可编辑 | auditor在阶段8生成 |
| **何时生成** | 阶段0起，贯穿全程 | 阶段8（终审时） |
| **粒度** | 12阶段 → 可逐步求精到子任务 | 可计量的质量标准 |
| **内容示例** | "#6 编码实现 → 🔄" | "圈复杂度≤15 ✅" |
| **断点再续** | ✅ 核心依据 | ❌ 不用于进度追踪 |
| **审计依据** | ✅ 配合journey.md | ✅ 质量达标证据 |
| **阶段结束标志** | ✅ 该阶段todo全部完成 | ❌ 只是质量闸门 |

## todo.md 核心机制

### WBS 逐步求精

```
阶段级（初始）：
  #4 coder: 架构设计 → 🔄
  #6 coder: 编码实现 → ⬜

子任务级（coder自行细化）：
  #4 coder: 架构设计 → 🔄
    #4.1 数据模型设计 → ⬜
    #4.2 API接口设计 → ⬜
    #4.3 可测试项清单 → ⬜
  #6 coder: 编码实现 → ⬜
```

### 谁可以建任务

| 场景 | 建任务者 | 任务承接者 | 示例 |
|------|---------|-----------|------|
| main派活 | main | 任意agent | "#4 coder: 架构设计" |
| agent细化 | agent自己 | 自己 | "#4.1 coder: 数据模型设计" |
| agent请求 | agent | 其他agent | "#6.1 tester: 性能压测" |

### 断点再续

```
最后一个完成的todo: #5 analyze ✅
下一个待开始的todo: #6 编码实现 ⬜ (承接: coder)
→ main 直接会话 coder: "继续项目X，阶段6编码，分支feature/xxx"
```

## journey.md 记录规范

每完成一行 todo，同时在 journey 追加一条：

```markdown
## YYYY-MM-DD

### HH:MM [coder] 阶段6编码启动
- todo #6 🔄，分支 feature/user-auth
- 基于 design.md 第3节实现

### HH:MM [coder] 阶段6编码完成
- todo #6 ✅
- 3个文件: UserAuth.java, AuthService.java, AuthController.java
- commit: a1b2c3d
```
