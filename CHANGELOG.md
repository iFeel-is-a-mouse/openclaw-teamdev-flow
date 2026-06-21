# MA 团队研发框架 — 变更日志

## [4.4.0] — 2026-06-21

### 角色重构
- codereviewer 更名为 reviewer
- main 五重身份精简为三重：顾问·教练·项目经理
- 总编辑职责移交 publicist，README 写作全权委派 publicist
- PM 改为项目经理，HR 角色删除，职责并入项目经理

### 文档清理
- 移除过程产物（RETROSPECTIVE.md、recurring-issues.md）
- CHANGELOG 精简，仅保留最新版
- 移除具体模型名称，改为优选/互补模型定性描述

## [4.3.0] — 2026-06-08

### 全量一致性审查 + 路由规则 + JSON 结构化

- **一致性问题修复**（基于三路 agent 联合审查）：
  - reviewer AGENTS.md 新增阶段4b设计审查流程（原缺失，P0）
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
- `projects/ma/reviewer/AGENTS.md` — 路由规则 + 阶段4b
- `projects/ma/auditor/AGENTS.md` — 路由规则 + 复盘审计
- `projects/ma/publicist/AGENTS.md` — 路由规则
- `projects/ma/CHANGELOG.md` — 本条目

（更早版本记录已归档）
