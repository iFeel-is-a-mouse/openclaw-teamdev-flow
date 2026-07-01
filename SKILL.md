---
name: team-dev
description: >
  Multi-agent team development workflow. Triggered by /team-dev or 团队研发.
  Orchestrates main/coder/reviewer/tester/auditor/publicist 6 roles for end-to-end software delivery.
  Core flow: requirements → complexity assessment → constitution → spec → audit → design → code → review → test → final audit → merge → docs.
  Emphasizes stage-based quality gates (auditor gate), design-first, test coverage, minimal changes.
intent: >
  team-development, multi-agent-orchestration, spec-driven, complexity-assessment,
  constitution, design-document, code-review, test-driven, audit-gate,
  checklist-based-quality, merge-gate, publicist-docs, waterfall-phases
metadata:
  author: iClaw
  version: "3.6.0"
  openclaw:
    emoji: "👥"
---

# team-dev — Multi-Agent Development Workflow

> Trigger: `/team-dev` or `团队研发` or `多agent开发` or `MA开发`
>
> User provides development requirements → orchestrates 6 specialist agents for end-to-end delivery.
>
> **Version**: 3.6.0

## Documentation Map

This is the **project root** for the team-dev skill. The actual skill file loaded by OpenClaw is at `skills/team-dev/SKILL.md`.

| Document | Purpose |
|----------|---------|
| `main/AGENTS.md` | Main agent team dev code of conduct (authoritative) |
| `main/SOUL.md` | Main agent team dev persona |
| `multi-agent-design.md` | Full architecture, config, communication matrix |
| `sequence-diagram.md` | End-to-end sequence diagrams |
| `SETUP.md` | Quick migration guide for new machines |
| `main/constitution-template.md` | Project constitution template |
| `main/todo-template.md` | Task board template |
| `main/journey-template.md` | Process journal template |
| `auditor/audit-report-template.md` | Issue registration / tracking / verification / closure |
| `tester/test-report-template.md` | Defect report & verification loop |

## Agent Roster

| Agent | Role |
|-------|------|
| **main** 🎓🏅📋 | Advisor · Coach · PM — sole user interface |
| **coder** 🏗️ | Architect · Driver — design + coding |
| **reviewer** 🔍 | Inspector · Navigator — code review |
| **tester** 🧪 | Tester — black-box + white-box + performance |
| **auditor** 🔒 | Auditor · Gatekeeper — pre-audit + final audit |
| **publicist** ✍️ | Writer — technical docs |
