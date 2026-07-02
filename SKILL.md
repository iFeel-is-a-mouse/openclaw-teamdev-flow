---
name: team-dev
description: Multi-agent team development workflow. Triggered by /team-dev or team development. Orchestrates 6 specialist agents for spec-to-delivery with quality gates.
metadata: { "openclaw": { "emoji": "👥" } }
---

# team-dev — Multi-Agent Development Workflow

> **Trigger**: `/team-dev` · `team development` · `multi-agent development` · `MA development`
>
> **Version**: 3.7.0
>
> **Runtime skill**: `workspace/skills/team-dev/SKILL.md` — §2 RSF is the authoritative process reference for all agents.

User provides requirements → 6 specialist agents deliver through staged gates. Each agent self-identifies their role and acts only on their stages (see RSF §2.1).

---

## Complexity Assessment

main evaluates at launch and declares S/M/L before execution begins.

| Dimension | S (Simple) | M (Medium) | L (Large) |
|-----------|-----------|-----------|-----------|
| Lines of Code | <200 | 200–1000 | >1000 |
| File Count | 1–3 | 4–10 | >10 |
| Modules/Interfaces | Single module | 2–3 modules | Multi-module/service |
| External Dependencies | 0–1 | 2–3 | Multiple |
| Security Sensitivity | None | Low | Medium/High |
| Requirement Ambiguity | Clear | Minor ambiguities | Multiple clarifications needed |

---

## Process Stages by Complexity

| Stage | S | M | L |
|-------|---|---|---|
| 0: Assessment + Constitution | ✅ | ✅ | ✅ |
| 1: Spec + Style Guide | ✅ | ✅ | ✅ |
| 2: Pre-Audit + Clarify | △① | ✅ | ✅ |
| 3: Main Gate Check | — | ✅ | ✅ |
| 4: Design + Testable Items | △② | ✅ | ✅ |
| 4b: Design Review (🔴 gate) | — | ✅ | ✅ |
| 5: Analyze Consistency | — | ✅ | ✅ |
| 6: Implementation | ✅ | ✅ | ✅ |
| 6b: Code Review | — | ✅ | ✅ |
| 7: Testing | ✅ | ✅ | ✅ |
| 8: Final Audit + Checklist | △③ | ✅ | ✅ |
| 9: Merge + README | ✅ | ✅ | ✅ |
| 10: Publicist Polish | — | — | ✅ |
| 11: Retrospective | — | — | ✅ |

① **S-level pre-audit**: main self-check replaces independent auditor (or lightweight auditor run).
② **S-level design**: verbal description only, no standalone `design.md`.
③ **S-level checklist**: simplified, ~8–12 auto-filtered items.

**M+ projects**: stage 0 includes best-practice search → collect into `examples/`. See runtime skill §4.0.5.

---

## Core Red Lines

From `main/AGENTS.md` §1.1. Inviolable — self-check before every action.

| # | Rule | Correct Approach |
|---|------|-----------------|
| 1 | Main does **not** code | Delegate to coder + reviewer |
| 2 | Main does **not** write docs | Delegate to publicist |
| 3 | Main does **not** audit | Delegate to auditor |
| 4 | Main does **not** test | Delegate to tester |
| 5 | Main **serves the user** | Respond promptly, don't get buried in tasks |

---

## Agent Roster

| Agent | Role | Workspace |
|-------|------|-----------|
| **main** 🎓🏅📋 | Advisor · Coach · PM — sole user interface | `workspace` |
| **coder** 🏗️ | Architect · Driver — design + coding | `workspace-coder` |
| **reviewer** 🔍 | Inspector · Navigator — code review | `workspace-reviewer` |
| **tester** 🧪 | Tester — black-box + white-box + perf | `workspace-tester` |
| **auditor** 🔒 | Auditor · Gatekeeper — pre-audit + final audit | `workspace-auditor` |
| **publicist** ✍️ | Writer — technical docs | `workspace-publicist` |

---

## Documentation Map

| Document | Purpose |
|----------|---------|
| `main/AGENTS.md` | Main controller code of conduct (authoritative) |
| `multi-agent-design.md` | Full architecture, agent design, communication matrix |
| `workspace/skills/team-dev/SKILL.md` §2 | RSF: Roles, Steps & Flows — authoritative interaction reference |
| `SETUP.md` | Environment setup, symlinks, initialization |
| `CHANGELOG.md` | Version history |
| `main/constitution-template.md` | Project constitution template (L-level) |
| `main/todo-template.md` | Task board template (WBS, resume from breakpoint) |
| `main/journey-template.md` | Process journal template |
| `auditor/audit-report-template.md` | Issue registration, tracking, verification, closure |
| `tester/test-report-template.md` | Defect report & verification loop |

> **Full workflow, agent AGENTS.md files, doc templates**: all under `projects/ma/`.
> **Runtime skill entry point** (loaded by OpenClaw): `workspace/skills/team-dev/SKILL.md`.
> **Quick start for new machines**: read `SETUP.md` first (symlinks are the #1 pitfall).
