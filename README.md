# MA — Multi-Agent Development Team

> A software development team composed of AI Agents. Each has a defined role, collaborates with others, enforces quality gates, and delivers reliably.

---

## Team Composition

| Agent | Role | One-Line Responsibility |
|-------|------|------------------------|
| **main** 🎓🏅📋 | Advisor · Coach · Project Manager | The user's sole interface: requirements entry, results exit, team dispatcher |
| **coder** 🏗️🖥️ | Architect · Driver | Design and coding, pair programming with reviewer |
| **reviewer** 🔍🧭 | Inspector · Navigator | Code review + pair programming, complementary to coder |
| **tester** 🧪 | Tester | Black-box + white-box + performance testing, ensuring code quality |
| **auditor** 🔒 | Auditor · Gatekeeper | Pre-audit + final audit + performance standards |
| **publicist** ✍️ | Writer | Technical documentation, copy editing |

## Workflow

```
User submits requirement
    │
    ▼
0 main: complexity assessment + process declaration
    │
    ▼
1 main: requirement analysis → auditor: pre-audit + clarify ──→ main: gate check
                                                                    │
                                                                    ▼
                                                              4 coder: design + technical proposal
                                                                    │
                                                                    ▼
                                                              5 analyze: consistency check
                                                                    │
                                                                    ▼
                                                        ┌─────────┴─────────┐
                                                        │  Pair Programming   │
                                                        │  coder ⟷ reviewer │
                                                        │  Driver/navigator   │
                                                        │  rotation           │
                                                        │  Real-time + formal │
                                                        │  review             │
                                                        └─────────┬─────────┘
                                                                    │
                                                                    ▼
                                                              7 tester: testing
                                                                    │
                                                                    ▼
                                                              8 auditor: final audit + checklist
                                                               │         │
                                                          issues found  ✅ passed
                                                               │         │
                                                               ▼         ▼
                                                          main: decide  main: ✅
                                                          iterate/fix  → merge
                                                                          │
                                                                          ▼
                                                                    9 main: README draft
                                                                          │
                                                                          ▼
                                                                    10 publicist: finalize docs
                                                                          │
                                                                          ▼
                                                                    11 retrospective (on demand)
```

> Core chain: `Requirement → Pre-audit → Design → [coder ⟷ reviewer pair programming] → Testing → Final Audit → Merge → Documentation`

## Quick Start

### 1. Create Agents

Create 6 agents in OpenClaw:

```bash
openclaw agents create coder    --workspace ~/.openclaw/workspace-coder
openclaw agents create reviewer --workspace ~/.openclaw/workspace-reviewer
openclaw agents create tester   --workspace ~/.openclaw/workspace-tester
openclaw agents create auditor  --workspace ~/.openclaw/workspace-auditor
openclaw agents create publicist --workspace ~/.openclaw/workspace-publicist
```

The main agent is your default agent and does not need to be created separately.

Create projects symlinks (all agents share the same directory):

```bash
for agent in coder tester auditor publicist; do
  ln -s ~/.openclaw/workspace/projects ~/.openclaw/workspace-$agent/projects
done
```

### 3. Initialize

Tell main:

> Study `SETUP.md`, synchronize and merge the respective AGENTS.md and SOUL.md for coder, tester, auditor, and publicist, and get them ready for team development.

main will read each role template, send them to the corresponding agent via `sessions_send`, and **merge (not overwrite)** into each agent's workspace. See `SETUP.md` for details.

### 4. Verify Configuration

Tell main:

> Team development: verify configuration, check if all agents are ready.

main will confirm each agent is reachable, symlinks are correct, and templates are synced. Once all checks pass, you're ready to begin.

### 5. Start Developing

Tell main what you want:

> Team development: help me build an XXX feature

main will automatically assess complexity, assign the appropriate process (S/M/L tier), and orchestrate the entire team to collaborate and deliver.

## Key Design Decisions

1. **Auditor covers the full lifecycle** — Pre-audit ensures requirement quality; final audit ensures delivery quality
2. **Filesystem is the communication bus** — All agents share the `projects/` directory, zero config, zero latency
3. **Constitution first** — Start every project by establishing constitution.md; all subsequent decisions defer to it as the supreme authority
4. **Model allocation by role** — main/coder/auditor use preferred models; reviewer/tester/publicist use complementary models
5. **Trust but verify** — main trusts agent capabilities, but every critical output must be reviewed

## Documentation Map

```
team-dev/
├── README.md                  ← You are here
├── SETUP.md                   ← Understanding, deployment & configuration guide
├── CHANGELOG.md               ← Version changelog
├── VERSION                    ← Current version number
├── multi-agent-design.md      ← Architecture design: Agent design, communication matrix, three-document system, configuration reference
├── sequence-diagram.md        ← Sequence diagrams: full-process interaction sequences, with todo.md and journey.md running through the entire process
├── docs/
│   ├── change-management.md   ← Change management full-process specification (CR registration / impact analysis / core file rules / data transformation specification)
│   └── testing-redline.md     ← Testing redline rules
├── skills/
│   └── SKILL.md               ← Team development execution process (Core Skill)
├── main/                      ← main agent configuration
│   ├── AGENTS.md              ← Team development code of conduct
│   ├── SOUL.md                ← Triple-identity persona
│   ├── constitution-template.md ← Project constitution template
│   ├── todo-template.md       ← Project task board template
│   └── journey-template.md    ← Project process log template
├── coder/
│   ├── AGENTS.md              ← Coding standards, design tiers
│   └── SOUL.md                ← Craftsmanship spirit
├── tester/
│   ├── AGENTS.md              ← Testing methodology
│   ├── SOUL.md                ← Professional skeptic
│   └── test-report-template.md ← Defect management + test report template
├── auditor/
│   ├── AGENTS.md              ← Triple audit + checklist
│   ├── SOUL.md                ← Impartial judge
│   └── audit-report-template.md ← Issue management + audit report template
└── publicist/
    ├── AGENTS.md              ← Writing rules
    └── SOUL.md                ← Literary flair, elegance
```

---

## Team Mottos

| Agent | Motto |
|-------|-------|
| **main** 🎓 | "The general must possess wisdom, credibility, benevolence, courage, and discipline." — Sun Tzu |
| **coder** 🏗️ | "Programs must be written for people to read, and only incidentally for machines to execute." — SICP |
| **tester** 🧪 | "Quality is not tested in, but quality that cannot be tested out is not quality." |
| **auditor** 🔒 | "Trust is good, auditing is better." |
| **publicist** ✍️ | "Expression that communicates is enough." — Confucius |

---
