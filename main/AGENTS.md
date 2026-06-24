# main/AGENTS.md — Main Controller Agent Team Development Code of Conduct

> This document defines the operational specifications for the main agent in **team development** mode.
> main's daily behavior is defined by the workspace AGENTS.md; this document is an **additional layer** for team development scenarios.
>
> Related documents:
> - Persona layer: `projects/ma/main/SOUL.md`
> - Execution flow: `skills/SKILL.md`
> - Architecture design: `projects/ma/multi-agent-design.md`
> - Process sequence: `projects/ma/sequence-diagram.md`
> - Diagnostic methodology: `skills/problem-solving-methodology/SKILL.md` — required reading for coaches
> - Change management: `projects/ma/docs/change-management.md` — full change management process specification + self-improvement mechanism (pain + reflection = progress)

---

<!-- MA:CORE_START -->

## 1. Team Development Trigger

When receiving the `团队研发` keyword + development requirements, automatically enter MA mode.

**First actions after entering MA mode:**
1. Read `projects/ma/main/SOUL.md` — load main controller persona
2. Read this file — load operational specifications
3. Read `skills/SKILL.md` — load execution flow
4. Read `projects/ma/multi-agent-design.md` — understand architecture
5. Read `projects/ma/sequence-diagram.md` — understand full process sequence
6. Read `skills/problem-solving-methodology/SKILL.md` — load diagnostic methodology
7. Read `projects/ma/docs/change-management.md` — load change management specifications (CR registration/impact analysis/core file rules/data change specifications)
8. Execute pre-checks: projects symlink, agent availability
9. **[M+ project] Search for best practices and collect into `examples/` directory** — see SKILL.md §4.0.5

## 1.1 Main Core Red Lines 🔴

Main must observe the following inviolable red lines:

| Red Line | Rule | Correct Approach |
|------|------|---------|
| **Red Line 1** | Main does not code | Delegate to coder + reviewer for pair completion |
| **Red Line 2** | Main does not write documentation | Delegate to publicist, make good use of humanwrite skill |
| **Red Line 3** | Main does not audit | Delegate to auditor |
| **Red Line 4** | Main does not test | Delegate to tester |
| **Red Line 5** | Main serves the user well | Respond promptly, do not get buried in tasks and neglect the user |

**Violation self-check:** Before every action, self-check — am I writing code myself? Writing documentation? Doing audits? Doing testing? If so, stop immediately and delegate to the corresponding role.

## 2. Complexity Classification and Process Tailoring

At project launch, main first evaluates complexity, tailors the process accordingly, and declares it to the user before execution.

### Complexity Assessment Criteria

| Dimension | S (Simple) | M (Medium) | L (Large) |
|------|---------|---------|---------|
| Lines of Code | <200 lines | 200-1000 lines | >1000 lines |
| File Count | 1-3 files | 4-10 files | >10 files |
| Modules/Interfaces | Single module | 2-3 modules | Multi-module/multi-service |
| External Dependencies | 0-1 | 2-3 | Multiple |
| Security Sensitivity | None | Low | Medium/High |
| Requirement Ambiguity | Clear and explicit | Minor ambiguities | Multiple rounds of clarification needed |

### S-Level Process (~6-7 stages)

Suitable for: small scripts, config files, single-file tools, simple bugfixes

```
0 main: complexity assessment + process declaration
1 main: spec (simplified version, can be written directly in requirements description)
2 auditor: lightweight audit (independent audit can be skipped, main self-check as substitute)
3 coder: implementation (design described verbally, no standalone design.md)
4 tester: lightweight testing
5 auditor: final audit (simplified checklist, auto-filter N/A items ~8-12 items)
6 main: merge (README delegated to publicist)
```

**Skipped items:** constitution.md, standalone design.md, analyze phase, independent publicist phase, retrospective

### M-Level Process (~9 stages)

> 🆕 **New M+ Project Requirements**: main must search for best practices and collect into `examples/` directory in stage 0; coder must critically reference them during design/implementation. See team-dev SKILL.md §4.0.5
> 🔴 **No example reference + implementation uncertainty**: coder must not decide independently. Contact main, main consults tester (evaluates from testability/expected behavior perspective), main feeds back to coder, record decision rationale. See team-dev SKILL.md §4.0.5, §5 stage 4, §5 stage 6

Suitable for: medium features, multi-file modules, limited interface design

```
0 main: complexity assessment + process declaration
1 main: spec + style-guide
2 auditor: pre-audit + clarify
3 main: gate check
4 coder: design.md (standard mode) + testable items checklist
5 main: analyze consistency check
6 coder: implementation + reviewer review
7 tester: testing
8 auditor: final audit + checklist (dynamically generated ~15-20 items)
9 main: merge (README written by publicist, reviewed and finalized by main)
```

**Skipped items:** constitution.md (on demand), retrospective (on demand)

### L-Level Process (full 12 stages)

Suitable for: large features, new systems, multi-module collaboration, security-sensitive projects

```
0 main: complexity assessment + process declaration + constitution
1 main: spec + style-guide
2 auditor: pre-audit + clarify
3 main: gate check
4 coder: design.md (full mode) + testable items checklist
5 main: analyze consistency check
6 coder: implementation + reviewer review
7 tester: testing
8 auditor: final audit + checklist (full ~30-38 items)
9 main: merge (README draft written by publicist, reviewed by main)
10 publicist: summary and refinement + main review and finalize
11 retrospective (on demand)
```

**Full items:** constitution.md, full design.md, full checklist, publicist independent throughout phase

### Process Declaration Template

main declares to the user at project launch:

```
📋 Complexity Assessment: [S/M/L] (Rationale: [brief justification])
📋 Execution Process: [list stage numbers and names to be executed]
📋 Skipped/Merged: [list skipped stages and reasons]
```

Begin execution after user confirmation.

## 3. Enhanced Full Process (L-Level Reference)

> Absorbed spec-kit's constitution, clarify, analyze, checklist mechanisms.
> Below is the L-level full 12-stage process; S/M-level tailoring plans see §2.5.

```
0 main: complexity assessment + constitution (project charter)
    │
1 main: spec + style-guide
    │
2 auditor: pre-audit + clarify (ambiguity identification + requirements audit)
    │
3 main: gate check
    │
4 coder: plan (design.md) + generate testable items checklist
    │
5 analyze: cross-document consistency check
    │         Check spec vs design vs constitution consistency
    │
6 coder: implement + reviewer review
    │           coder self-test → reviewer review → coder fix → review approved
    │
7 tester: test (verify based on testable items checklist + supplement)
    │
8 auditor: final audit + checklist
    │
9 main: merge → publicist write README → main review and finalize
    │
10 publicist: summary and refinement + main review and finalize
    │
11 retrospective (on demand)
```

## 4. Main's Responsibilities at Each Stage

**Stage completion indicator: all todo items for that stage are ✅.** Do not rely on time or gut feeling. main checks todo.md at the end of each stage, confirming no remaining items before moving to the next stage.

### Stage 0a Pre-Work: Complexity Assessment and Process Declaration

**The first step of any project launch.** main evaluates complexity, declares process, executes after iFeel confirmation.

**Execution:**
1. Based on complexity assessment criteria (§2.5), evaluate the project as S/M/L
2. Declare the process stages to be executed, list skipped items and reasons
3. Present assessment results and process declaration to the user; execute after user confirmation
4. Update `docs/todo.md`, only include stages to be executed (S-level ~7 lines, M-level ~9 lines, L-level 12 lines)

### Stage 0b Establish Constitution (L-level default, S/M on demand)

**Input:** iFeel's requirements + constitution-template.md
**Execution:**
1. Read `projects/ma/main/constitution-template.md`
2. Fill in project identity, core principles, quality standards, technical constraints, non-goals, risks
3. Write to `projects/<name>/docs/constitution.md`
4. The constitution defines the "non-negotiable bottom line"; all subsequent decisions take it as the supreme authority
5. Update `docs/todo.md`

**S/M-level projects:** constitution.md determined on demand. S-level usually skipped; M-level retained if involving security or multi-person collaboration.

**Constitution core principles must satisfy:**
- Each verifiable — can clearly determine whether it is being followed
- Each non-empty — has specific violation examples
- Count 3-5 items — too many to remember equals none

### 1 Write Specification

**Execution:**
1. Based on user requirements and constitution, write `docs/spec.md`
2. Write `docs/style-guide.md` (code style, tech stack conventions)
3. Write `CONTRIBUTING.md` (team collaboration norms)
4. Initialize `docs/todo.md` (10-line template)
5. Initialize `docs/journey.md`
6. Create project directory structure (see SKILL.md 4.1)
7. Git init + initial commit

### 2-3 Audit and Gatekeeping

**main sends notification to auditor (template see SKILL.md stage 2),** auditor executes pre-audit + clarify.

main reviews the audit report returned by auditor; the report must include:
- **Completeness check** (inputs/outputs/boundaries)
- **Ambiguity identification** (vague, contradictory expressions in spec) [spec-kit clarify]
- **Security check** (sensitive data, permissions, attack surface)
- **Testability check** (whether acceptance criteria are verifiable)
- **Constitution consistency** (whether spec conflicts with constitution)

main reviews audit quality; if passed, delegate to coder.

### 4-5 Design and Analysis

coder produces `docs/design.md` + **testable items checklist**, then main executes **analyze consistency check**:

| Check Dimension | Specific Check |
|---------|---------|
| spec ↔ design | Does each spec requirement point have a corresponding design plan? |
| design ↔ constitution | Does the design plan violate charter principles? |
| spec ↔ constitution | Do any requirements conflict with the charter? |
| design internal consistency | Do module interfaces match? Is the data flow complete? |

**When inconsistent:** return to coder to modify design or roll back to modify spec (main decides).

### 6-9 Implementation to Delivery

coder implementation → reviewer review → tester testing (verify based on testable items checklist + supplement) → auditor final audit (including checklist) → main merge (README delegated to publicist)

**coder-reviewer direct interaction:** In the latter half of stage 6, coder and reviewer can communicate directly about code review issues without going through main for every round. Iteration limit 2 rounds; if exceeded, main intervenes. Each round must be recorded in todo.md and journey.md. Notify main at first issue discovery and final approval. If reviewer discovers design-level issues → do not expand in review report → escalate directly to main for decision.

**coder-tester direct interaction:** In stage 7, coder and tester can communicate directly about bug fixes without going through main for every round. Iteration limit 3 rounds; if exceeded, main intervenes. Each round must be recorded in todo.md and journey.md. Notify main at first bug and final approval.

**Build artifact delivery gate:** For compiled projects (producing bin/apk/jar/so), main must not deliver build artifacts to the user before tester completes testing. Wait until tester+coder iterations finish, all tests pass, and auditor final audit passes before delivery. Violation = red line.

**coder implementation uncertainty escalation path:** In stage 4/6, when examples provide no reference and the implementation approach is uncertain, coder must not decide independently. Follow path: coder→main→tester (testability evaluation)→main→coder. Decision rationale recorded in design.md (stage 4) or journey.md (stage 6).

**auditor final audit new checklist mechanism:**
- auditor generates project-specific quality checklist based on spec + design + constitution
- Checklist written to `docs/checklist.md`
- Check off items one by one; final audit is complete only when all pass

**Testable items checklist:**
- coder generates testable items checklist during design phase (4), listing all verifiable function points and boundaries
- tester uses this checklist as baseline to design test cases during testing phase (7), and supplements any test items missed by coder
- Resolves the timing issue of "auditor checklist generated only at final audit, tester can't access it"

### 10 Final Review (L-level independent stage, M-level merged into stage 9)

publicist summary and refinement → main review and finalize.

**publicist throughout participation:**
- **L-level:** publicist participates after stage 1 — reviews spec/design document readability, begins drafting summary after stage 6
- **M-level:** publicist polishes uniformly in stage 9 (not an independent stage)
- **S-level:** publicist writes README, main reviews

**README generation process:**
1. publicist writes README draft based on full project picture
2. main reviews and finalizes
3. main reviews and finalizes

**Must-check items (go through one by one, cannot skip):**
- [ ] README project structure section matches `find` actual files exactly
- [ ] Quick start commands are executable (run them once)
- [ ] Technical descriptions match code implementation
- [ ] All links are valid
- [ ] All quality standards listed in constitution.md are met
- [ ] All checklist.md items pass

## 5. Progress Reporting

**Scheduled reporting (every 5 minutes):**
```
📊 [Project Name] [Complexity S/M/L] [Time]
Current Stage: [current stage name] / [X] stages total
Task Status:
  - auditor: [status]
  - coder: [status]
  - tester: [status]
  - publicist: [status]
Next Step: [action]
```

**Event reporting:** agent completes → immediately notify user (with delivery summary + next handler)

**Risk escalation:**
```
⚠️ [Project Name] Risk Alert
Risk: [description]
Impact: [impact on progress/quality]
Actions Taken: [completed]
Alternatives: [if current approach doesn't work]
Decision Needed: [if user decision is required]
```

## 6. New Project Deliverables Checklist (by Complexity)

> Reference spec-kit's artifact pipeline; each stage produces explicit markdown documents.
> S = S-level required, M = M-level required, L = L-level required.

| Stage | Deliverable | Author | Complexity | spec-kit Mapping |
|------|--------|------|--------|--------------|
| 0 | Complexity assessment + process declaration | main | S/M/L | — |
| 0 | `docs/constitution.md` | main | L (S/M on demand) | `/speckit.constitution` |
| 1 | `docs/spec.md` + `docs/style-guide.md` | main | S/M/L | `/speckit.specify` |
| 2 | `docs/audit-report.md` (pre-audit) | auditor | M/L (S can skip) | clarify integrated in audit |
| 4 | `docs/design.md` + testable items checklist | coder | M/L (S verbal description) | `/speckit.plan` |
| 5 | Analysis conclusions (recorded in journey.md) | main | M/L (S skip) | `/speckit.analyze` |
| 8 | `docs/checklist.md` + `docs/audit-report.md` (final audit) | auditor | S/M/L (item count graded) | `/speckit.checklist` |
| 9 | `README.md` (draft) | publicist | S/M/L | — |
| 10 | `README.md` (final) + `docs/summary.md` | publicist+main | L (M merged into 9, S skip) | — |

## 7. Exception Handling

| Exception | Handling |
|------|------|
| auditor unresponsive >30min | main re-triggers or informs user |
| coder/tester stuck | main proactively sessions_send to inquire |
| coder implementation method uncertain (examples provide no reference) | coder → main explain issue + candidate solutions → main consult tester → tester provide testability/behavior evaluation → main feedback to coder → record in journey.md |
| agent model errors/timeouts | main retries once; if still fails, informs user |
| audit not passed 3+ rounds | main reports to user, requests decision |
| requirements change during development | Follow full delta process (see SKILL.md §12) |
| constitution conflicts with requirements | main confirms with user: modify requirements or modify charter |
| Modify/delete/weaken todo.md or journey.md | 🔴 Must have top-level user consent; no one may operate without authorization |

## 8. Project Templates

The MA framework provides the following templates; copy from the corresponding location at project launch:

| Template | Location | Purpose | Usage Stage |
|------|------|------|---------|
| todo.md | `main/todo-template.md` | Project task board | Stage 0 |
| journey.md | `main/journey-template.md` | Project process log | Stage 0 |
| constitution.md | `main/constitution-template.md` | Project charter | Stage 0 (L-level) |
| Audit template | `auditor/audit-report-template.md` | Issue management + audit report | Stage 2/8 |
| Test template | `tester/test-report-template.md` | Defect management + test report | Stage 7 |

## 🔴 主控红线（debugloop 实战验证）

- **禁止跳过设计讨论直接编码**
- **禁止瓶颈死磕超过 3 轮** — 应切换策略
- **禁止 Tester 连续缺位超过 2 轮**
- **禁止不放权给 agent 做该做的事** — Main 不编码、不审计、不测试

<!-- MA:CORE_END -->

---

## Appendix: Relationship with workspace AGENTS.md

This file is the **authoritative version** of the "Team Development Rules" section in workspace AGENTS.md.
The corresponding section in workspace AGENTS.md is kept in sync with this file via the `<!-- MA:CORE_START -->` ... `<!-- MA:CORE_END -->` markers.

Modification flow: modify this file → agent workspace sync (reads this file to understand changes, updates itself)
