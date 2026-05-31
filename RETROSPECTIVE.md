# 复盘报告 — MA 团队研发经验教训

> 持续更新的复盘文档 | 最近更新: 2026-05-24

---

## 第三次复盘 — 示例项目A 流程记录缺失（journey.md + todo.md）

> 日期: 2026-05-24 | 项目: 示例项目A | 复盘人: auditor + main

### 问题一：journey.md 阶段缺失

journey.md 8 个标准阶段只记录了 4 个（14完成，2重复，5不完整），阶段3678完全缺失。覆盖度仅 50%。

### 问题二：todo.md 模板不完整

todo.md 模板只列了 5 项任务（前置审计、编码、测试、终审、文档），缺少：
- 1 main 需求分析
- 3 main 把关
- 7 main merge
- 8 main 终审
- 9 事后复盘

根因相同：**main 和 auditor 自己的操作没有被纳入任务跟踪。**

### 根因

**SKILL.md 和 agent AGENTS.md 都没有把"主控操作"写进必须步骤。**

- SKILL.md 的 todo.md 模板只有 5 项，main 自己的需求分析、把关、merge、终审都没有登记
- journey.md 同样——每个阶段的 agent 任务模板里缺少"写入 journey.md"步骤
- 导致 main 完成了把关/merge/审阅，但没有记录，也没有被追踪

### 修复

| # | 修复项 | 文件 |
|---|--------|------|
| 1 | todo.md 模板从 5 项扩到 10 项（覆盖 1-9 全部阶段） | `skills/team-dev/SKILL.md` |
| 2 | SKILL.md 每个阶段的 agent 任务模板新增"写入 journey.md"步骤 | `skills/team-dev/SKILL.md` |
| 3 | SKILL.md 新增阶段9"事后复盘"和主体/客体区分原则 | `skills/team-dev/SKILL.md` |
| 4 | auditor AGENTS.md 新增"写入 journey.md"操作 | `projects/ma/auditor/AGENTS.md` |
| 5 | coder AGENTS.md 新增"写入 journey.md"操作 | `projects/ma/coder/AGENTS.md` |
| 6 | tester AGENTS.md 新增"写入 journey.md"操作 | `projects/ma/tester/AGENTS.md` |
| 7 | publicist AGENTS.md 新增"写入 journey.md"操作 | `projects/ma/publicist/AGENTS.md` |
| 8 | MA README 工作流图新增阶段9 | `projects/ma/README.md` |
| 9 | main AGENTS.md 进度简报模板 8→9 | `AGENTS.md` |
| 10 | 同步到 agent workspace | workspace-auditor/coder/tester/publicist |
| 11 | 4 个 agent AGENTS.md 新增"交付前检查清单"章节（checkbox 格式） | projects/ma/*/AGENTS.md |

### agent 反馈亮点

**coder 的洞察（已固化为 MA 原则）：**
> "main 做 gate check。" — main 不是流水线上的一环，是每个阶段的把关者。agent 交付 → main 审 → 放行或退回。

**publicist 的洞察（已固化为 MA 原则）：**
> "todo.md 是 plan（要做什么），journey.md 是 log（做了什么）。每完成一行 todo 就记一条 journey。两个文件保持时间线上的对应关系。"

这条表述简洁、可操作、可审计，已写入 SKILL.md 4.3 节。

### 教训

1. **"所有人写入"="没人写入"。** 流程文档中说"所有 agent 写入"太模糊，必须具体到每个 agent 的任务模板里。
2. **主控操作也要被追踪。** main 的需求分析、把关、merge、终审是工作流的一部分，不是"后台操作"，必须纳入 todo.md 和 journey.md。
3. **保留现场，不改项目。** 示例项目A 的 journey.md 和 todo.md 是历史记录，缺失就缺失。复盘改的是 MA 组织流程文档（主体），不是已完成的开发项目（客体）。

---

## 第二次复盘 — 示例项目A README 完整性缺失

> 日期: 2026-05-24 | 项目: 示例项目A | 复盘人:  main

### 问题

README.md 的"项目结构"章节遗漏了：
- `docs/todo.md` — 任务跟踪文件（高优先级）
- 5 张测试截图 png（中等优先级）
- `scripts/` 和 `tests/` 空目录（低优先级）

所有遗漏文件实际存在，只是 README 没写。由 auditor 事后审计发现。

### 根因

**三层防线全部失效：**
1. **Publicist** — 写 README 时凭印象列文件，没跑 `find` 核实实际项目结构
2. **Main（总编辑）** — 审阅 publicist 交付时没做文件结构对比
3. **Auditor（终审）** — AGENTS.md 审计清单不含"README 项目结构完整性"

深层次根因：流程文档中 README 完整性检查未显式化，依赖 agent 自觉而非流程约束。

### 修复

| # | 修复项 | 文件 |
|---|--------|------|
| 1 | publicist 自审流程加入"第零遍：事实核查"——README 项目结构必须 `find` 确认 | `projects/ma/publicist/AGENTS.md` |
| 2 | publicist 红线新增：不允许 README 项目结构与实际文件不一致 | `projects/ma/publicist/AGENTS.md` |
| 3 | auditor 交付终审清单新增"README 完整性"检查项 | `projects/ma/auditor/AGENTS.md` |
| 4 | team-dev SKILL.md 阶段8 main 终审加入逐项检查清单（含 README 结构一致性） | `skills/team-dev/SKILL.md` |
| 5 | 示例项目A README.md 项目结构更新，补全遗漏文件 | `projects/示例项目A/README.md` |

### 教训

**凭印象写文档就是埋雷。** 任何文档中的"文件列表"、"项目结构"、"目录树"，必须跑一次实际文件列表。这不是多此一举——人（和 AI）的记忆不可靠，文件系统是唯一真相源。

---

## 第一次复盘 — 示例项目B 团队研发演示

> 日期: 2026-05-23 | 项目: 示例项目B | 复盘人:  main 
> 总耗时: ~11 分钟 | Agent 调用: 6 次 (auditor×3, coder×1, tester×1, publicist×0)

---

## 一、时间线

```
22:24  项目初始化 → auditor 前置审计（第1轮）
22:25  ❌ 第1轮审计失败 — 跨workspace文件访问问题
22:25  修复：建立 projects 软链接
22:27  auditor 前置审计（第2轮）→ ✅ 通过
22:29  coder 架构设计+编码 → ✅ 完成
22:30  main 教练审查通过
22:32  tester 测试 → ✅ 27/27 全通过
22:33  auditor 交付终审 → ✅ 通过
22:35  git merge → 交付
       ❌ publicist 遗漏 — README.md 和项目总结未撰写
```

## 二、问题清单

### P0 — 阻断性问题

| # | 问题 | 根因 | 影响 | 修复状态 |
|---|------|------|------|---------|
| 1 | **auditor 首轮审计失败** | agent workspace 独立，看不到 main 的 projects 目录 | 浪费一次审计轮次（2分钟） | ✅ 已修复：建立了软链接；文档已更新（AGENTS.md, SKILL.md, SETUP.md） |
| 2 | **publicist 遗漏** | 流程设计缺陷：todo.md 模板不含 publicist 任务；SKILL.md 中"如有写作需求"措辞暗示可选 | README.md 和项目总结未产出 | ✅ 已修复：SKILL.md 将 publicist 改为必须步骤；todo.md 模板新增 #5 任务 |

### P1 — 重要问题

| # | 问题 | 根因 | 影响 | 修复状态 |
|---|------|------|------|---------|
| 3 | **tester 无法直接通知 auditor** | 子 agent session 缺少 sessions_send 的 target 信息 | tester 只能通过 main 转交，多一次 handoff | ✅ 确认为设计（main 转手模式），文档已更新 |
| 4 | **agent AGENTS.md 版本不同步** | coder workspace 用的是旧版目录规范（doc/pm/log/）；未建立模板→部署的同步机制 | agent 行为可能不一致 | ✅ 已修复：同步了所有 agent workspace 的 AGENTS.md/SOUL.md |
| 5 | **main 缺四重身份描述** | main 的 AGENTS.md 团队研发章节不够完整 | main 执行团队研发时角色认知可能不足 | ✅ 已修复：AGENTS.md 新增四重身份和参考文档链接 |

### P2 — 改进建议

| # | 问题 | 建议 |
|---|------|------|
| 6 | **模型配置不一致** | AGENTS.md 说 tester/auditor 用 flash，实际 config 全部用 pro。应统一或说明原因 |
| 7 | **main coach 审查偏快** | coder 提交后我只花了几秒看代码就通过了。重大项目应该更严格 |
| 8 | **缺少 agent 健康检查** | 没有在项目启动前确认所有 agent 可用、workspace 正常 |

## 三、流程分析

### 做得好的

- **auditor 终审质量高**：272 行审计报告，涵盖需求追溯、代码审查、测试审查、流程合规、安全审计 OWASP Top 10
- **tester 测试全面**：黑盒 4 + 白盒 19 + 边界 4 = 27 项检查，全部通过
- **coder 设计文档详尽**：142 行 design.md，含 ADR 记录，即便项目只有一行代码
- **journey.md 记录完整**：时间线清晰，每步有据可查

### 流程瓶颈

```
耗时分析：
  审计失败+修复:  2 min  (本可避免)
  auditor 前置:   1 min
  coder 编码:     1 min
  tester 测试:    2 min
  auditor 终审:   2 min
  main 调度开销:  3 min  (等待、审查、手递手交接)
```

- **手递手交接是最大瓶颈**：tester 完成→main 收到→main 派 auditor，每次交接多一轮等待
- 如果 agent 间可直接通信（coder→tester→auditor），耗时可能减半

## 四、经验教训

1. **软链接是第一道坎。** 任何新机器部署，projects 软链接不建立，整个 MA 流程直接瘫痪。必须在文档头三条写明，并在每次团队研发启动时做前置检查。

2. **publicist 不是可选的。** "如有写作需求"这个措辞害了流程。代码+文档才算交付，文档必须和编码一样是必须步骤。

3. **agent 间直接通信不可靠。** 设计上 tester→auditor 直接通信，但实际执行中 tester 报告"缺少 target"。当前架构下 main 做 handoff 是唯一可靠方案。

4. **模板和部署要同步。** projects/ma/ 的 AGENTS.md 模板改了，但 agent workspace 里没更新。需要建立同步机制或至少定期检查。

5. **简单项目也不能跳步。** 示例项目B 极简，但流程中的每个问题（软链接、publicist、agent 通信）都是在这个极简项目里暴露的。简单项目是最好的流程验证。

## 五、行动项

| # | 行动 | 优先级 | 状态 |
|---|------|--------|------|
| 1 | 所有 MA 文档加入软链接前置检查 | P0 | ✅ 已完成 |
| 2 | SKILL.md / todo.md 模板将 publicist 改为必须 | P0 | ✅ 已完成 |
| 3 | main AGENTS.md 补充四重身份描述 | P0 | ✅ 已完成 |
| 4 | 同步所有 agent workspace 的 AGENTS.md/SOUL.md | P0 | ✅ 已完成 |
| 5 | 创建 SETUP.md 迁移指南 | P0 | ✅ 已完成 |
| 6 | 创建本复盘报告 RETROSPECTIVE.md | P1 | ✅ 本文档 |
| 7 | tester→auditor 走 main 转手（设计如此） | P1 | ✅ 已确认（文档已更新为 main 转手模式） |
| 8 | 添加 agent 启动前健康检查机制 | P2 | ⬜ 待设计 |
| 9 | 模型配置统一（flash vs pro） | P2 | ⬜ 待决策 |

---

*"不打无准备之仗，不做不复盘之事。" — main 教练*
