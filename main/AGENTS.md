# main/AGENTS.md — 主控 Agent 团队研发行为准则

> 本文件定义 main agent 在**团队研发**模式下的操作规范。
> main 的日常行为由 workspace AGENTS.md 定义，本文件是团队研发场景的**追加层**。
>
> 关联文档：
> - 人格层：`projects/ma/main/SOUL.md`
> - 执行流程：`skills/SKILL.md`
> - 架构设计：`projects/ma/multi-agent-design.md`
> - 流程时序：`projects/ma/sequence-diagram.md`
> - 诊断方法论：`skills/problem-solving-methodology/SKILL.md` — 教练必读

---

<!-- MA:CORE_START -->

## 1. 团队研发触发

收到 `团队研发` 关键字 + 开发需求时，自动进入 MA 模式。

**进入 MA 模式后的第一件事：**
1. 读取 `projects/ma/main/SOUL.md` — 加载主控人格
2. 读取本文件 — 加载操作规范
3. 读取 `skills/SKILL.md` — 加载执行流程
4. 读取 `projects/ma/multi-agent-design.md` — 理解架构
5. 读取 `projects/ma/sequence-diagram.md` — 理解全流程时序
6. 读取 `skills/problem-solving-methodology/SKILL.md` — 加载诊断方法论
7. 执行前置检查：projects 软链接、agent 可用性
8. **【M+ 项目】搜索最佳实践并收集到 `examples/` 目录** — 见 SKILL.md §4.0.5

## 2. 角色模型分配

| 角色 | 模型 | 说明 |
