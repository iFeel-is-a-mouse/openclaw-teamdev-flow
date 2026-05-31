# MA — Multi-Agent 开发团队

> 一支由 AI Agent 组成的软件开发团队。各司其职，相互协作，质量把关，交付可靠。
>
> **版本：v3.0** | 发布日期：2026-05-25 | 文档：《团队研发管理办法及实施指引》
>
> **v3.0 里程碑：todo.md 和 journey.md 确立为项目宪法核心，贯穿全部12阶段，5 agent 透明可编辑。**

---

## 团队构成

| Agent | 角色 | 一句话职责 |
|-------|------|-----------|
| **main** 🎓🏅📋👥✍️ | 顾问·教练·PM·HR·总编辑 | 用户的唯一接口，需求入口，结果出口，团队调度 |
| **coder** 🏗️💻 | 架构师·编码 | 设计技术方案，编写高质量代码 |
| **tester** 🧪 | 测试 | 黑盒+白盒测试，保证代码质量 |
| **auditor** 🔒 | 审计·守门员 | 需求前置审计 + 交付终审，质量最后一道闸 |
| **publicist** ✍️ | 写手 | 技术文档、散文随笔、润色修改 |

## 工作流

```
用户提需求
    │
    ▼
0 main 制定宪章 → docs/constitution.md [NEW]
    │
    ▼
1 main教练 提炼 → auditor 前置审计+clarify ──→ main教练 把关
                                                          │
                                                          ▼
                                                    4 coder 架构设计
                                                          │
                                                          ▼
                                                    5 analyze 一致性检查 [NEW]
                                                          │
                                                          ▼
                                                    6 coder 编码实现
                                                          │
                                                          ▼
                                                    7 tester 测试
                                                          │
                                                          ▼
                                                    8 auditor 终审+checklist [NEW]
                                                     │         │
                                                 有问题     ✅ 通过
                                                     │         │
                                                     ▼         ▼
                                                main教练    main ✅
                                                决策迭代    → merge
                                                              │
                                                              ▼
                                                    9 publicist 写作
                                                         README + 总结
                                                              │
                                                              ▼
                                                    10 main 审阅
                                                         → 用户
                                                              │
                                                              ▼
                                                    11 auditor + main
                                                        事后复盘
                                                    修改 MA 流程文档

README流程：main(9)初稿 → publicist(10)总结完善 → main(10)定稿
写作任务：main @publicist → publicist 基于初稿写作 → main 审阅定稿 → 发布
```

## 关键设计决策

1. **Auditor 覆盖完整生命周期** — 前置审计（含 clarify 歧义识别）确保需求质量，终审（含 checklist 质量清单）确保交付质量
2. **Auditor 通过 main教练 行使影响** — 发现问题报告 main教练，不直接指挥 coder/tester
3. **宪法先行** [NEW] — 项目启动先定 constitution.md，所有后续决策以此为最高依据
4. **Analyze 把关** [NEW] — 设计完成、编码开始前，执行跨文档一致性分析
5. **Checklist 收尾** [NEW] — auditor 生成项目特定的质量检查清单，逐项验证后放行
6. **所有产出物在 `projects/`** — Git 管理，新增 add，修改 checkout，完成 commit
7. **深度任务用 Pro，常规任务用 Flash** — main/coder/publicist 用 deepseek-v4-pro，tester/auditor 用 deepseek-v4-flash
8. **同一台机器共享文件系统** — 不需要各自 clone，零配置

## 设计依据

| Agent | 核心参考 |
|-------|---------|
| coder | 设计模式(GoF)、代码大全(McConnell)、DDD(Evans)、重构(Fowler)、NASA JPL 编码标准、SRE(Google)、人件 |
| tester | 软件工程、黑盒测试7法、白盒测试6标准、NASA 验收标准 |
| auditor | PMP(PMBOK)、ISA 审计原理、OWASP Top 10、NASA-STD-8739.8、IEEE 1012 |
| publicist | 梁启超作文规矩、Technical Writing、Strunk & White |

## 文档导航

```
projects/ma/
├── README.md                  ← 你在这里
├── SETUP.md                   ← 理解与配置指南
├── RETROSPECTIVE.md            ← 复盘报告
├── multi-agent-design.md      ← 完整架构设计
├── main/                      ← main agent 团队研发配置 [NEW]
│   ├── AGENTS.md              ← main 团队研发行为准则
│   ├── SOUL.md                ← main 团队研发人格（五重身份）
│   └── constitution-template.md ← 项目宪章模板
├── coder/
│   ├── AGENTS.md              ← 编码规范、设计层次
│   └── SOUL.md                ← 工匠精神、系统思维
├── tester/
│   ├── AGENTS.md              ← 测试方法论、Bug 报告模板
│   └── SOUL.md                ← 职业怀疑者、解谜者
├── auditor/
│   ├── AGENTS.md              ← 三重审计+clarify+checklist
│   └── SOUL.md                ← 公正法官、证据主义者
└── publicist/
    ├── AGENTS.md              ← 写作规矩、文体规范
    └── SOUL.md                ← 书卷气、文采、长期主义
```

## 快速开始

```bash
# 已创建的 agent
openclaw agents list

# 查看某个 agent 的配置
openclaw agents list --json | python3 -c "import json,sys; [print(a['id'], a['workspace']) for a in json.load(sys.stdin)]"
```

**新机器部署：** 见 [SETUP.md](SETUP.md)（理解后自行配置）。

**团队研发 Skill：** `skills/team-dev/SKILL.md` — main agent 执行团队研发时的操作流程。

**⚠️ 关键配置：** 每个 agent workspace 必须有 `projects` 软链接指向 main workspace 的 projects 目录。

---

*"信任是好的，审计更好。" — auditor 格言*
