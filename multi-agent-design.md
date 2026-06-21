# Multi-Agent Architecture Design

> Status: Active | Version: v3.6 | Author: MA Team
>
> **Related Documents:**
> - Sequence Diagram: `sequence-diagram.md` — Full-process interaction sequences (woven through todo.md and journey.md)
> - Execution Skill: `skills/SKILL.md` — Team R&D operational workflow
> - Deployment Guide: `SETUP.md` — Environment configuration and initialization
> - Templates: `main/todo-template.md`, `main/journey-template.md`, `auditor/audit-report-template.md`, `tester/test-report-template.md`

---

## 1. Overall Architecture

```
┌───────────────────────────────────────────────────────────────┐
│                           User                                │
│                            │                                  │
│                            ▼                                  │
│  ┌───────────────────────────────────────────────────────┐   │
│  │                  main (Controller/Coordinator)          │   │
│  │   🎓 Advisor · 🏅 Coach · 📋 Project Manager           │   │
│  │   Model: preferred model                               │   │
│  └───┬───────┬──────────┬───────────────┬────────────┘   │
│      │       │          │               │                 │
│      │  Pre-audit       │               │                 │
│      │       ▼          │               │                 │
│  ┌───┴──────────────┐   │               │                 │
│  │   auditor 🔒     │   │               │                 │
│  │  Pre-audit +     │   │               │                 │
│  │  Final Audit     │   │               │                 │
│  │  Model: preferred│   │               │                 │
│  └───────┬──────────┘   │               │                 │
│          │ Passed        │               │                 │
│          ▼              │               │                 │
│  ┌──────────────────────────────┐       │                 │
│  │     Pair Programming         │       │                 │
│  │  ┌──────────┐   ⟷   ┌──────┐│       │                 │
│  │  │  coder   │ Pair  │ code ││       │                 │
│  │  │  🖥️ Driver │══════╡ review││       │                 │
│  │  │ moonshot  │Rotate │ 🔍   ││       │                 │
│  │  └─────┬────┘   └──┬───┘│       │                 │
│  └────────┼────────────┼────┘       │                 │
│           │Self-test   │Review     │                 │
│           │passed      │passed      │                 │
│           ▼            ▼            │                 │
│  ┌──────────┐                      │                 │
│  │  tester  │                      │                 │
│  │  Testing │                      │                 │
│  │  Model: complementary model     │                 │
│        │ Tests passed    │               │                 │
│        ▼                 │               │                 │
│  ┌──────────────┐        │               │                 │
│  │ auditor 🔒   │        │               │                 │
│  │ Final Audit  │        │               │                 │
│  │              │        │               │                 │
│  │ Issues? ─────┼────────┼───────────────┼──→ main decides │
│  │ Passed? ─────┼────────┼───────────────┼──→ main ✅ → User│
│  └──────────────┘        │               │                 │
│                          │               │                 │
│                ┌─────────┴──┐            │                 │
│                │ publicist  │            │                 │
│                │ Writer ✍️   │            │                 │
│                │ Model: complementary  │                 │
│                └────────────┘            │                 │
└───────────────────────────────────────────────────────────────┘

⚠️ auditor finds issues → main decides (does not directly command coder/tester)
⚠️ reviewer finds issues → interacts directly with coder to fix (max 2 rounds) → escalate to main beyond limit
```

**Core Flow:** `Requirements → auditor Pre-audit → coder Design → reviewer Design Review (🔴 Gate) → coder Implementation → reviewer Code Review → tester → auditor Final Audit → main ✅ → User`

**Full-process sequence details:** `sequence-diagram.md`

---

## 2. Agent Design

### 2.1 main — Controller

| Attribute | Value |
|-----------|-------|
| **agentId** | `main` (default agent) |
| **Model** | `preferred model` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace` |

**Three Roles:** Advisor (requirements analysis) → Coach (quality gatekeeping) → Project Manager (progress scheduling) → Reviewer (documentation gatekeeping)

**Detailed Code of Conduct:** `main/AGENTS.md` | **Persona Definition:** `main/SOUL.md`

### 2.2 coder — Architect & Driver

| Attribute | Value |
|-----------|-------|
| **agentId** | `coder` |
| **Model** | `preferred model` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-coder` |

**Multiple Roles:**
- 🖥️ **Driver** — Writes code implementation; the "driver" in pair programming
- 🏗️ **Architect** — Design first; responsible for technical approach and system architecture
- 📊 **Performance Design** — Defines project performance targets (response time, throughput, concurrency); engineers must specify performance metrics in design.md
- 🧭 **Navigator** — Rotates role with reviewer; acts as navigator when reviewing the other's code

**Pair Programming:** Direct interaction with reviewer, rotating Driver/Navigator roles. Different models complement: coder (preferred model) excels at implementation, reviewer (complementary model) excels at logic review. Max 3 rounds; escalate to main beyond limit.

**Sub-agent Mode (maxSpawnDepth: 2):** For complex requirements, spawns a sub-agent to design, then spawns another sub-agent to implement.

**Detailed Code of Conduct:** `coder/AGENTS.md` | **Persona Definition:** `coder/SOUL.md`

### 2.3 tester — Testing

| Attribute | Value |
|-----------|-------|
| **agentId** | `tester` |
| **Model** | `complementary model` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-tester` |

**Responsibilities:** Test Design → Execution → Bug Report (direct interaction with coder, max 3 rounds) → Regression Verification → Submit for Audit

**Extended Responsibilities:**
- **Performance Test Execution** — Executes performance test plans, outputs performance reports
- Performance standards set by auditor; performance metrics specified in coder's design

**Bug Report Template:** `tester/test-report-template.md` | **Detailed Code of Conduct:** `tester/AGENTS.md`

### 2.4 auditor — Audit

| Attribute | Value |
|-----------|-------|
| **agentId** | `auditor` |
| **Model** | `preferred model` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-auditor` |

**Triple Audit:** Completion Check + Process Compliance Audit + Performance Standard Setting
- **Pre-audit** (includes ambiguity identification via clarify)
- **Final Audit** (includes checklist quality verification; shifted from code review to review-of-review)
- **Performance Standards** — Defines project performance testing standards; determines if performance meets targets

**Security:** Security review is not currently top priority; reviewer performs basic security scanning during pair programming; auditor does not conduct dedicated security audits.

**Issue Management Template:** `auditor/audit-report-template.md` | **Detailed Code of Conduct:** `auditor/AGENTS.md`

### 2.5 publicist — Writer

| Attribute | Value |
|-----------|-------|
| **agentId** | `publicist` |
| **Model** | `complementary model` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-publicist` |

**Responsibilities:** Technical documentation, essays, polishing/editing. Communicates only with main.

**Detailed Code of Conduct:** `publicist/AGENTS.md` | **Persona Definition:** `publicist/SOUL.md`

### 2.6 reviewer — Code Reviewer & Navigator

| Attribute | Value |
|-----------|-------|
| **agentId** | `reviewer` |
| **Model** | `complementary model` |
| **Tool Profile** | `coding` |
| **Workspace** | `~/.openclaw/workspace-reviewer` |

**Three Roles:**
- 🔍 **Design Reviewer** — After coder produces design.md, verifies code evidence accuracy against source code (method existence, line numbers, file locations). 🔴 Mandatory gate for M/L-level projects; coder may not proceed to implementation without passing
- 🔍 **Code Reviewer** — Reviews code quality (functionality, readability, security, performance) after coder completes self-testing
- 🧭 **Navigator** — Pair programs with coder, rotating Driver/Navigator roles; reviews while coding

**Dual Review Mechanism (from Change #14 experience):**
1. **Design Review (Stage 4b):** coder produces design.md → reviewer verifies item by item against source code → returns to coder for fixes if not passing
2. **Code Review (Stage 6b):** coder implementation complete → reviewer reviews code quality → direct interaction with coder to fix issues found

**Collaboration Mode:**
- coder ⟷ reviewer direct interaction (pair programming, max 3 rounds) → escalate to main beyond limit
- Uses different models (coder=preferred model, reviewer=complementary model) for complementary strengths
- coder may consult reviewer in real time for uncertain design decisions
- reviewer finds issues during review → discusses fix approach directly with coder

**Detailed Code of Conduct:** `reviewer/AGENTS.md` | **Persona Definition:** `reviewer/SOUL.md`

---

## 3. Communication Matrix

```
        ┌────────┬────────┬────────┬─────────┬───────────┬──────────────┐
        │  main  │ coder  │ tester │ auditor │ publicist │ reviewer     │
────────┼────────┼────────┼────────┼─────────┼───────────┼──────────────┤
 main   │   -    │  ↔️     │  ↔️     │  ↔️      │    ↔️      │     ↔️        │
 coder  │  ↔️     │   -    │  ↔️     │  ↔️      │    —      │     ↔️        │
 tester │  ↔️     │  ↔️     │   -    │  ↔️      │    —      │     —        │
auditor │  ↔️     │  ↔️     │  ↔️     │   -     │    —      │     —        │
publicist│ ↔️    │   —    │   —    │   —     │    -      │     —        │
reviewer│ ↔️ │  ↔️    │   —    │   —     │    —      │     -        │
────────┴────────┴────────┴────────┴─────────┴───────────┴──────────────┘

publicist communicates only with main (sole source of writing requests)
reviewer communicates with main and coder (review closed loop); no direct interaction with tester/auditor
All other agents fully interconnected
```

**Key Interaction Rules:**
- main ↔ all agents (dispatch center)
- coder ↔ reviewer (code review closed loop, max 2 rounds)
- coder ↔ tester (bug fix closed loop, max 3 rounds)
- auditor finds issues → main decides (does not directly command coder/tester)
- tester → main → auditor (audit relayed through main)
- reviewer finds design issues → escalate to main for adjudication (not expanded in review report)

---

## 4. Three-Document System

| Document | Essence | Written By | Lifecycle |
|----------|---------|------------|-----------|
| **todo.md** | Task Board (WBS) | 5 agents | Stage 0 → project end |
| **journey.md** | Process Log | 5 agents | Stage 0 → project end |
| **checklist.md** | Quality Verification Checklist | auditor | Stage 8 |

**Templates:** `main/todo-template.md`, `main/journey-template.md`

**Detailed Description:** `skills/SKILL.md` §Three-Piece Set

---

## 5. Project Directory Convention

```
project/
├── docs/                   ← All documents centrally managed
│   ├── constitution.md     ← main output, project constitution (L-level)
│   ├── spec.md             ← main output, requirements specification
│   ├── design.md           ← coder output, architecture design
│   ├── style-guide.md      ← main output, code style guide
│   ├── test-report.md      ← tester output, test report
│   ├── audit-report.md     ← auditor output, audit report
│   ├── audit-issues.md     ← auditor output, issue tracking register
│   ├── bugs.md             ← tester output, bug tracking register
│   ├── checklist.md        ← auditor output, quality verification checklist
│   ├── todo.md             ← maintained by each agent, task board
│   └── journey.md          ← written by each agent, process log
├── src/                    ← Source code
├── tests/                  ← Test files
├── scripts/                ← Build/run scripts
├── README.md               ← publicist output
├── CHANGELOG.md            ← Version changelog
├── CONTRIBUTING.md         ← Contribution guide
├── code-review-report.md   ← reviewer output, code review report (optional)
└── .gitignore
```

Tailor `src/` and `tests/` subdirectory structure per language; see `SETUP.md`.

---

## 6. Configuration Design

### 6.1 Agent Configuration Reference (YAML)

```yaml
agents:
  defaults:
    subagents:
      maxSpawnDepth: 2
      maxChildrenPerAgent: 5
      runTimeoutSeconds: 1800

  list:
    - id: "main"
      default: true
      workspace: "~/.openclaw/workspace"
      model: "<preferred model>"

    - id: "coder"
      workspace: "~/.openclaw/workspace-coder"
      model: "<preferred model>"

    - id: "tester"
      workspace: "~/.openclaw/workspace-tester"
      model: "<complementary model>"

    - id: "auditor"
      workspace: "~/.openclaw/workspace-auditor"
      model: "<preferred model>"

    - id: "publicist"
      workspace: "~/.openclaw/workspace-publicist"
      model: "<complementary model>"

    - id: "reviewer"
      workspace: "~/.openclaw/workspace-reviewer"
      model: "<complementary model>"

tools:
  agentToAgent:
    enabled: true
    allow: ["main", "coder", "tester", "auditor", "publicist", "reviewer"]

bindings:
  - agentId: "main"
    match:
      channel: "*"
      accountId: "*"
```

### 6.2 Projects Symlink

Every agent workspace must have a `projects` symlink pointing to the main workspace:

```bash
for agent in coder tester auditor publicist reviewer; do
  ln -s ~/.openclaw/workspace/projects ~/.openclaw/workspace-$agent/projects
done
```

**This is the #1 pitfall for new machine deployment.** See `SETUP.md`.

---

## 7. Known Limitations

### 7.1 Inter-Agent Communication: Main Relay Pattern

tester completes tests → sessions_send → main → main sessions_spawn auditor. Each relay introduces ~30-60 seconds of delay, without affecting correctness.

### 7.2 Template Synchronization

`projects/ma/` is the template repository; agent workspaces are deployment targets. After modifying templates, main must sync via a merge mechanism (see `SETUP.md` §Initialization).

### 7.3 Model Cost

2-3x token consumption increase compared to a single agent. auditor only triggers after tester passes, avoiding idle runs.

### 7.4 Sub-session Lifecycle

**Subagents are not reusable** — Each `sessions_spawn` creates a brand-new child session, destroyed after completion (enters sessions store after announce).

**Configuration File:** `openclaw.json`
```json5
{
  agents: {
    defaults: {
      subagents: {
        maxSpawnDepth: 2,
        maxChildrenPerAgent: 20,
        runTimeoutSeconds: 3600,  // ← 60-minute timeout
      },
    },
  },
  session: {
    maintenance: {
      mode: "enforce",
      pruneAfter: "3d",   // clean sessions older than 3 days
      maxEntries: 50,      // max 50 entries retained per agent
    },
  },
}
```

**Maintenance Rules:**
- `pruneAfter: "3d"` — Sessions older than 3 days are automatically cleaned
- `maxEntries: 50` — Each agent retains at most 50 session records
- Running sub-sessions are unaffected
- Subagent enters sessions store after completion; cleaned on next maintenance trigger

---

## 8. Document Change Process

### 8.1 Core Principle

**All documents may only be modified incrementally (append/revise); never overwrite the entire file from scratch.**

### 8.2 Modification Rules

| Document Type | Modification Method | Example |
|--------------|---------------------|---------|
| AGENTS.md / SOUL.md | Append new paragraphs, supplementary notes | Append pair programming paragraph under collaboration notes |
| multi-agent-design.md | Revise affected sections, increment version number | Modify §2.6 model, v3.6→v3.7 |
| spec.md / design.md | Append change records, retain original text | Append requirements change records at document bottom |
| todo.md / journey.md | Append line by line; do not delete completed items | Append new entries at end of file |
| README.md | Append new feature paragraphs | Append new features after feature list |

### 8.3 Red Lines (Non-Violable)

🔴 **Forbidden: using `cp` or full-file overwrite to update files across agent workspaces.**

Even when MA project template files are updated, precise merging must be done using the `edit` tool:
1. First `read` the original file in the workspace
2. Locate the specific paragraphs to modify
3. Use `edit` to replace those paragraphs
4. If a full-file overwrite is truly necessary, **first back up the original file with git commit**, then overwrite

**Consequence of Violation:** Direct overwrite loses custom configuration and personalized content in the workspace, causing information loss.

### 8.4 Forbidden Behaviors

- ❌ `git rm` then recreate the file
- ❌ Overwrite with `> file` redirection
- ❌ Full-file overwrite with `cp` without backup
- ❌ Replace with complete file content (e.g., AI generating an entire new file to replace the original)
- ❌ Start writing without reading the original file content first

### 8.5 Correct Process

1. **Read First** — Use `read` to understand the file's complete content
2. **Locate** — Find the paragraph positions that need modification
3. **Precise Edit** — Use `edit` to replace target paragraphs, or `write` to append to file end
4. **Verify** — Confirm the modified file content is reasonable and no context is lost
5. **Commit** — `git add` + `git commit`, with commit message indicating what was changed and where

1. **Read First** — Use `read` to understand the file's complete content
2. **Locate** — Find the paragraph positions that need modification
3. **Precise Edit** — Use `edit` to replace target paragraphs, or `write` to append to file end
4. **Verify** — Confirm the modified file content is reasonable and no context is lost
5. **Commit** — `git add` + `git commit`, with commit message indicating what was changed and where
