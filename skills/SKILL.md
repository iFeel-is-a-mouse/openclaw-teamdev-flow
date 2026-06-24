---
name: ma-team-dev
name-zh: 团队研发
provider: openclaw
description: >
  Multi-Agent team collaborative development skill.
  Triggered when the user says "团队研发" (team development) or "多agent开发" (multi-agent development).
  When the main agent receives a development request, it orchestrates
  coder/reviewer/tester/auditor/publicist — six specialized agents
  based on complexity (S/M/L) to deliver end-to-end.
  Applicable to: new feature development, bug fixes, refactoring, design changes, documentation.
  Not applicable to: pure Q&A, simple scripts (below S-level can be handled by main alone),
  specialized tasks covered by existing dedicated skills.
metadata:
  version: "4.3.0"
  skills-version: "1.0"
  trigger-keywords:
    - 团队研发
    - 多agent开发
    - MA开发
    - 团队协作开发
  # Agent routing table — which agent OpenClaw routes to when the user says specific keywords
  # Each agent's AGENTS.md must also configure corresponding forwarding rules
  agents:
    orchestrator: main          # Master controller, responsible for scheduling and routing
    designer: coder              # Architecture design and coding implementation
    reviewer: reviewer       # Code review and design review
    tester: tester               # Testing
    auditor: auditor             # Auditing
    writer: publicist            # Documentation writing
  routing:
    - trigger: "团队研发"
      target: main
      action: "main evaluates complexity then orchestrates coder/reviewer/tester/auditor/publicist"
      note: "Other agents must forward to main when receiving this keyword"
    - trigger: "多agent开发"
      target: main
      action: "Same as 团队研发"
      note: "Same as 团队研发"
  routing-redirects:
    - if_agent: coder
      trigger: "团队研发"
      action: "redirect to main: 'User wants to start team development, I've forwarded to main'"
    - if_agent: tester
      trigger: "团队研发"
      action: "redirect to main: 'User wants to start team development, I've forwarded to main'"
    - if_agent: reviewer
      trigger: "团队研发"
      action: "redirect to main: 'User wants to start team development, I've forwarded to main'"
    - if_agent: auditor
      trigger: "团队研发"
      action: "redirect to main: 'User wants to start team development, I've forwarded to main'"
    - if_agent: publicist
      trigger: "团队研发"
      action: "redirect to main: 'User wants to start team development, I've forwarded to main'"
---

# Team Development — Multi-Agent Collaborative Development Skill

## Overview

Orchestrate 6 specialized AI Agents (main/coder/reviewer/tester/auditor/publicist) into a virtual development team,
completing software development projects through an industrialized workflow. Each Agent performs its role, with quality checks at every level to deliver reliable code.

### What It Does
- User submits a development request → main evaluates complexity → orchestrates the corresponding workflow → agents collaborate to deliver
- Covers: requirements analysis, architecture design, code review, test verification, audit gatekeeping, documentation output

### What It Does NOT Do
- Pure utility scripts, one-line config changes, and other below-S-level tasks (main handles these directly)
- Specialized tasks already covered by independent Skills (e.g., database migration, API integration)
- Architecture disputes requiring human decision (main escalates to user for decision)

### When to Use
| Use When | Don't Use When |
|----------|----------------|
| New feature → "Team dev: add user registration" | Simple change → "Change the title to red" |
| Bug fix → "Team dev: login page returns 500" | Pure Q&A → "What's the difference between Java 8 and 11?" |
| Refactoring → "Team dev: refactor payment module" | Existing Skill → "Help me with database migration" (use db-migrate Skill) |
| Design change → "Team dev: order state machine needs adjustment" | Emergency fix → main handles and delivers directly, backfill process afterwards |

### Core Advantages
- **Layered quality gates**: reviewer review → tester testing → auditor final audit, three lines of defense
- **Complexity-adaptive**: S(Simple)/M(Medium)/L(Large) three-tier workflow, small projects run fast, large projects proceed steadily
- **Continuous knowledge accumulation**: journey.md records the process, retrospectives distill lessons learned

---

## Environment and Prerequisites

Before executing this Skill, main must confirm the following conditions are met:

### Environment Check

```bash
# 1. Projects symlink check (all agent workspaces must have access to the projects directory)
for ws in workspace-coder workspace-tester workspace-auditor workspace-publicist; do
  target="$HOME/.openclaw/$ws/projects"
  if [ ! -e "$target" ]; then
    echo "❌ $ws: projects symlink missing"
    exit 1
  fi
  echo "✅ $ws: projects OK"
done

# 2. Agent availability check
for agent in coder reviewer tester auditor publicist; do
  echo "→ Checking if $agent is reachable..."
  # main quickly confirms agent responsiveness via sessions_spawn
done
```

**Only after prerequisites are met** should you proceed to the complexity assessment stage. If conditions are not met, fix the environment first before starting.

---

## 3. Execution Workflow

### Workflow Overview

After main receives a requirement, it follows the workflow matching the complexity level. See §2.5 for assessment criteria.

**Complexity → Workflow Mapping:**

```
main evaluates complexity
  If LOC < 200 AND files ≤ 3 AND single module AND no external dependencies → S-level
  If LOC 200-1000 OR files 4-10 OR 2-3 modules → M-level
  If LOC > 1000 OR files > 10 OR multi-module OR security-sensitive → L-level
  If uncertain → default to the higher level
```

**Stages by Complexity Level:**

| Stage | S-Level (Simple) | M-Level (Medium) | L-Level (Large) | Description |
|------|------------------|------------------|-----------------|-------------|
| 0 main: complexity assessment | ✅ | ✅ | ✅ | Must execute, declare workflow for user confirmation |
| 0 main: constitution | Skip | On demand | ✅ | Project constitution, defining non-negotiable principles |
| 1 main: spec | Merge into requirement description | ✅ | ✅ | Includes style-guide |
| 2 auditor: pre-audit | main self-check instead | ✅ | ✅ | Includes clarify ambiguity identification |
| 3 main: gate check | Skip | ✅ | ✅ | Review self-check list, delegate to coder |
| 4 coder: design.md | Verbal description | ✅ | ✅ | M+ level: must provide examples for data transformations |
| 4b reviewer: design review | Skip | 🔴 Gate | 🔴 Gate | Verify design + data transformation examples |
| 5 main: analyze | Skip | ✅ | ✅ | spec↔design↔constitution consistency |
| 6 coder: implementation | ✅ | ✅ | ✅ | Includes self-testing |
| 6b reviewer: code review | Skip | 🔴 Gate | 🔴 Gate | Pair programming review |
| 7 tester: testing | Lightweight testing | ✅ | ✅ | Based on testable items checklist |
| 8 auditor: final audit | Simplified checklist | ✅ | ✅ | Full checklist |
| 9 main: merge | ✅ | ✅ | ✅ | → publicist README → main review |
| 10 publicist: finalize | Skip | Merge into 9 | ✅ | Documentation summary and refinement |
| 11 retrospective | Skip | On demand | ✅ | Five-step retrospective method |

**Stage Abbreviation Quick Reference:**
- ✅ = Must execute
- 🔴 Gate = Cannot proceed to next stage if not passed
- Skip = This stage is not executed
- On demand = Decided based on actual project circumstances

### Inter-Stage Checkpoints

After each stage is completed, main performs the following checkpoint to confirm readiness for the next stage:

```markdown
## Checkpoint Quick Reference

| Stage | Pass Condition | Check Command/Step |
|-------|---------------|-------------------|
| 0 (declaration) | User confirms workflow declaration | User replied with confirmation message |
| 1 (spec) | User confirms requirements in spec.md | User confirms no ambiguity |
| 2 (audit) | Auditor pre-audit passed, no unresolved CLARIFY items | Audit report has no P0/P1 |
| 3 (gate) | Self-check list review passed | main confirms all "yes" items have been elaborated in design |
| 4 (design) | Reviewer design review passed | Review conclusion is ✅ |
| 5 (analyze) | spec↔design↔constitution has no contradictions | main confirms consistency |
| 6b (review) | Reviewer code review passed | Review conclusion is ✅ or conditionally passed |
| 7 (testing) | All tests passed, no P0/P1 bugs | Tester test report pass rate = 100% |
| 8 (final audit) | Auditor checklist all ✅ | Checklist has no failed items |
| 9 (merge) | CHANGELOG updated, CR closed | git merge successful |
```

### Handling Checkpoint Failures

| Failed Stage | Problem Type | Handling | Roles Involved |
|-------------|-------------|----------|----------------|
| Stage 4b (design review) | Design issue | coder revises design.md → reviewer re-reviews | coder, reviewer |
| Stage 6b (code review) | Coding issue | coder fixes → reviewer re-reviews (direct interaction) | coder, reviewer |
| Stage 7 (testing) | Coding issue | coder fixes → tester regression verification (direct interaction) | coder, tester |
| Stage 2 (pre-audit) | Requirement issue | main confirms ambiguity with user → main modifies spec | main |
| Stage 8 (final audit) | Audit issue | coder fixes item by item → auditor performs new final audit | coder, auditor |
| **Same stage fails 2 consecutive rounds** | Escalation | main escalates to user for decision | main + user |

**Direct Interaction Rules:** In stages 6b/7, coder interacts directly with reviewer/tester without going through main as relay. Update todo.md/journey.md each round. Iteration cap: 2 rounds (review) or 3 rounds (testing). Exceeding the cap triggers main intervention.

---

## 0. Skills Dependency Declaration

In team development mode, the following skills are shared by all agents:

| Skill | Purpose | Required Readers |
|-------|---------|-----------------|
| `problem-solving-methodology` | Structured problem diagnosis: dissolution layer→define→diagnose→solution→verify→retrospect | main, coder, tester |
| `team-dev` (this skill) | Multi-agent collaborative orchestration | All |
| `code-review-expert` | Multi-dimensional code review methodology | reviewer |

**External Feedback Rule:** After an agent uses a skill in a task, it must note in `journey.md` or the delivery summary which skill and which part of its methodology was used. E.g., "Root cause confirmed via problem-solving three-question method."

---

## 1. Trigger and Entry

After the user says "团队研发" (team development), include the development requirement (or main follows up to confirm the requirement). Main acts as master controller/coach/project manager, responsible for full workflow scheduling.

## 2. Team Roles

> See `projects/ma/multi-agent-design.md` — Agent design, communication matrix, model assignment.

## 2.5 Complexity Assessment and Workflow Tailoring

### 2.5.1 Why Complexity Tiers Are Needed

Not every project needs an 11-stage pipeline. Running a 50-line config script through the full workflow wastes resources.
Complexity tiers match the workflow to project scale — small projects run fast, large projects proceed steadily.

### 2.5.2 Complexity Assessment Criteria

| Dimension | S (Simple) | M (Medium) | L (Large) |
|-----------|-----------|-----------|-----------|
| Lines of Code | < 200 lines | 200-1000 lines | > 1000 lines |
| File Count | 1-3 files | 4-10 files | > 10 files |
| Modules/Interfaces | Single module | 2-3 modules | Multi-module/multi-service |
| External Dependencies | 0-1 | 2-3 | Multiple |
| Security Sensitivity | None | Low | Medium/High |
| Requirement Ambiguity | Clear and explicit | Minor ambiguity points | Requires multiple clarifying rounds |

**Assessment Principle:** Use the highest dimension as the determining factor. For example, M-level LOC but L-level security sensitivity → execute at L-level.
When uncertain, default to the higher level.

### 2.5.3 S-Level Workflow (~6-7 stages)

Suitable for: small scripts, config files, single-file utilities, simple bugfixes, README updates

```
0 main: complexity assessment + workflow declaration
1 main: spec (simplified, can be merged into requirement description)
2 auditor: lightweight audit (main self-check instead, no independent auditor session delegation)
3 coder: implementation (design described verbally or in 1-2 paragraphs, no standalone design.md)
4 tester: lightweight testing
5 auditor: final audit (simplified checklist, auto-filter N/A items → ~8-12 items)
6 main: merge (README delegated to publicist)
```

**Skipped Items and Rationale:**
| Skipped Item | Rationale |
|-------------|-----------|
| constitution.md | Simple projects don't need constitutional governance |
| Standalone design.md | Verbal description / 1-2 paragraphs is sufficient |
| analyze stage | No standalone design means no analyze subject |
| Publicist standalone stage | Publicist writes README |
| Retrospective | Simple projects don't need retrospective |

### 2.5.4 M-Level Workflow (~9 stages)

Suitable for: medium features, multi-file modules, moderate interface design

```
0 main: complexity assessment + workflow declaration
1 main: spec + style-guide
2 auditor: pre-audit + clarify
3 main: gate check
4 coder: design.md (standard mode, N/A sections allowed to be omitted) + testable items checklist
4b reviewer: design review — verify design evidence accuracy against source code 🔴 Gate
5 main: analyze consistency check
6 coder: implementation
6b reviewer: code review — pair programming review of implementation code
7 tester: testing
8 auditor: final audit + checklist (dynamically generated → ~15-20 items)
9 main: merge (README written by publicist, reviewed and finalized by main)
```

**Skipped Items and Rationale:**
| Skipped Item | Rationale |
|-------------|-----------|
| constitution.md | On demand (keep when security/multi-person collaboration is involved) |
| Publicist standalone stage | Merged into stage 9, not an independent stage |
| Retrospective | Triggered on demand |

### 2.5.5 L-Level Workflow (Full 11 stages)

Suitable for: large features, new systems, multi-module collaboration, security-sensitive projects

```
0 main: complexity assessment + workflow declaration + constitution
1 main: spec + style-guide
2 auditor: pre-audit + clarify
3 main: gate check
4 coder: design.md (full mode) + testable items checklist
4b reviewer: design review — verify design evidence accuracy against source code 🔴 Gate
5 main: analyze consistency check
6 coder: implementation
6b reviewer: code review — pair programming review of implementation code
7 tester: testing
8 auditor: final audit + checklist (full ~30-38 items)
9 main: merge (README delegated to publicist)
10 publicist: summary and refinement + main review and finalization (throughout participation)
11 retrospective (on demand)
```

**Fully Enabled Items:** constitution.md, full design.md, full checklist, publicist independent throughout-participation stage

### 2.5.6 Workflow Declaration

At project startup (before stage 0), main must make an explicit workflow declaration to the user:

```
📋 [Project Name] Complexity Assessment
Complexity: [S/M/L]
Assessment Rationale: [Brief description, e.g., "3 files, single module, no external dependencies → S-level"]
Execution Workflow:
  0 main: complexity assessment ✅
  1 main: spec
  2 auditor: pre-audit
  ... (list all stages to be executed)
Skipped Items:
  - constitution.md: S-level projects don't need constitutional governance
  - Retrospective: simple projects don't need retrospective
Confirmed, begin execution.
```

After user confirmation, main writes the workflow declaration as the first entry in `docs/journey.md`.
## 3. Full Workflow (L-Level Reference)

> The following is the complete L-level 11-stage workflow. See §2.5 for S/M-level tailoring.

### Main's Triple Identity

As main, you must simultaneously play three roles in this multi-person collaboration:

**🎓 Advisor** — When the user presents a requirement, you don't passively receive it; you actively analyze.
- Is this requirement reasonable? Is there a better approach?
- Tech stack suggestions: desktop tools use Python, Web uses JS/TS, Java projects as needed.
- Evaluate complexity (S/M/L), declare workflow per level, and tailor stages [ENHANCED: P4]
- **Don't rush into action; first determine whether delegation is needed.** If you can quickly complete it yourself, don't mobilize the team unnecessarily.

**🏅 Coach** — You are the head coach of this "AI team"; you don't play on the field yourself.
- Review auditor's pre-audit quality — did the auditor miss anything? Was the audit rigorous enough?
- Review coder's design proposal — is the architecture reasonable? Is there over-engineering or under-design?
- Review tester's test quality — is coverage sufficient? Are bug reports clear enough?
- Make decisions after receiving auditor's issue feedback: iterate and fix / accept the status quo / abandon the requirement.
- **Coaching is not micromanaging.** You tell the agent what result you want, not how to write every line.
- **Coaching helps the team improve.** When you notice an agent repeatedly making the same type of mistake, provide improvement suggestions.

**📋 Project Manager (PM)** — Responsible for progress visibility to the user. This is the role that most directly impacts user experience.
- **Scheduled updates (every 5 minutes):** When there's progress, report status and next steps; when there's no progress, a one-line brief; when there are risks, elaborate on countermeasures and plans.
- **Event notifications:** Immediately notify the user whenever an agent completes work (with delivery summary and next handler).
- Maintain docs/todo.md, tracking all task statuses
- Coordinate inter-agent dependencies
- Proactively query agents that haven't responded for over 30 minutes
- Writing tasks → @publicist
- **No silence:** The user should never have to guess "who's doing what right now" — always keep them informed.

**🔍 Reviewer** — You perform final review after publicist delivers.
- Confirm documentation accurately reflects technical content
- Confirm writing style meets project requirements
- Formatting and layout, sanitization check

## 3. Full Workflow (L-Level Reference)

> The following is the complete L-level 11-stage workflow. See §2.5 for S/M-level tailoring.

```
User submits requirement
    │
    ▼ (0 Pre-stage)
main complexity assessment → workflow declaration → user confirmation ✅
    │  S/M/L see §2.5
    ▼
0 main drafts constitution → docs/constitution.md [NEW: spec-kit]
    │  (S-level skip, M-level on demand)
    ▼
1 main analyzes requirements → writes docs/spec.md + docs/style-guide.md
    │
    ▼
2 auditor pre-audit + clarify (ambiguity identification + requirement audit) [ENHANCED: spec-kit]
    │  (S-level: main self-check instead)
    ▼
3 main reviews audit results → formally delegates to coder
    │
    ▼
4 coder designs architecture → docs/design.md + testable items checklist
    │  (S-level: verbal description, no standalone design.md)
    ▼
4b reviewer design review 🔴 Gate [NEW: Change #14 experience]
    │     Verify design document code evidence accuracy against source code item by item
    │     Check API existence, method signatures, file locations, line numbers
    │     Not passed → return to coder for correction → re-review
    │     🔴 coder must not enter stage 6 implementation until reviewer review passes
    │  (S-level skip, M/L-level mandatory)
    ▼
5 analyze cross-document consistency check [NEW: spec-kit]
    │     main checks spec ↔ design ↔ constitution consistency
    │  (S-level skip)
    ▼
6 coder coding implementation → self-test
    │
    ▼
6b reviewer code review → pair programming review of implementation code
    │     coder ↔ reviewer direct interaction (cap 2 rounds) → escalate to main if exceeded
    │     🔴 Design review already passed; this stage focuses on code correctness, security, readability
    │  (S-level skip, M/L-level mandatory)
    ▼
7 tester black-box + white-box testing → bug report → coder fix → regression
    │
    ▼
8 auditor delivery final audit + checklist [ENHANCED: spec-kit]
    │     Generate docs/checklist.md → verify item by item → trace against baseline
    │  (Dynamically generated, filter N/A items by project characteristics)
    ▼
9 main final confirmation → git merge (README delegated to publicist)
    │  (S-level: publicist writes README)
    ▼
10 publicist summary and refinement + main review finalization
    │  (S-level skip, M-level merged into 9, L-level throughout participation)
    ▼
11 post-hoc retrospective — auditor audit process record → main retrospective → modify MA workflow docs
```

**Core Principles:**
- **Only when auditor approves can you report completion to the user. Don't let unaudited work reach the user.**
- **Every agent must report progress to the user after completing work.** main is responsible for forwarding or notifying. Don't keep the user waiting.
- **main must report progress to the user at least every 5 minutes.** Even if there are no major changes, send a brief status update.

## 4. Project Initialization

After receiving a "团队研发" (team development) request, main first executes:

### 4.0 Complexity Assessment [NEW: P4]

1. Evaluate project complexity as S/M/L according to the criteria in §2.5
2. Declare the workflow stages to be executed and skipped items based on complexity
3. Present assessment results and workflow declaration to the user, wait for confirmation
4. After user confirmation, write the workflow declaration as the first entry in `docs/journey.md`
5. Initialize `docs/todo.md` based on the tailored workflow

**S-Level Workflow Notes:** spec can be merged into the requirement description; design can skip a standalone document; publicist does not participate.
main creates `docs/todo.md` + `docs/journey.md` in the project root directory and execution can begin. Templates are at `projects/ma/main/todo-template.md` and `projects/ma/main/journey-template.md`.

**M-Level Workflow Notes:** constitution decided on demand. publicist polishes documents in stage 9, not as an independent stage.

**L-Level Workflow Notes:** Follow the full 11 stages, including constitution and independent publicist throughout participation.

### 4.0.5 External Component Research and Best Practice Collection [NEW: M+ projects mandatory]

**Applicable Level: M-level and above**

Before writing the spec, main must:

1. **Search the internet for best practices**: For the project's core technical domain (UI framework, architecture patterns, third-party libraries, etc.), search for current industry best practices and code examples
2. **Collect into the `examples/` directory**:
   ```bash
   mkdir -p projects/<project-name>/examples
   ```
   Organize search results into markdown files (e.g., `android-ui-best-practices.md`), containing:
   - Design pattern descriptions
   - Code examples
   - ⚠️ Critical points (applicable conditions, potential pitfalls)

3. **Serve as coder's design reference**:
   - coder must read the `examples/` directory before stage 4 (design) and stage 6 (coding)
   - coder must **accept dialectically and critically** — example code is reference, not copy-paste material
   - coder must note in design.md or code comments which decisions referenced which parts of examples
   - 🔴 **No example reference + implementation uncertainty**: coder does not decide independently. Contact main, main consults tester (evaluating from testability/expected behavior perspective), main feeds back to coder, record decision basis (stage 4 → design.md, stage 6 → journey.md)

4. **Allow reuse of external components**: M+ projects may introduce mature external libraries/components, but must provide selection rationale in design.md

**Example**: Android UI project → search "Android toolbar status bar transparent best practice", "adjustResize keyboard layout pattern", etc., organize as examples/android-ui-best-practices.md

### 4.1 Create Project Directory

```bash
mkdir -p projects/<project-name>/{src,tests,scripts,bin,lib,docs}
```

> See §4.5.1 for Git initialization and .gitignore.

### 4.2 Write Specification Documents

- **`docs/constitution.md`** — Project constitution, non-negotiable principles. Populated based on `projects/ma/main/constitution-template.md` [NEW]
- **`docs/spec.md`** — Project goals, functional scope, non-functional requirements (performance, security), acceptance criteria
- **`docs/style-guide.md`** — Language version, naming conventions, formatting requirements, comment language, prohibited practices
- **`CONTRIBUTING.md`** — Team collaboration conventions (branching strategy, commit conventions, code review process)

**constitution.md Writing Guidelines [NEW]:**
1. Based on template `projects/ma/main/constitution-template.md`
2. 3-5 core principles, each verifiable, with violation examples
3. Technical constraints listing non-optional choices (language, build system, target platform)
4. Non-goals explicitly listing what this project does NOT do
5. The constitution is the supreme reference for all subsequent decisions — auditor audits and coder designs must align with the constitution

### 4.3 Initialize Tracking Files (MA Core Trio)

MA projects have three core tracking files, each with its own function:

| File | Essence | Who Writes | Purpose |
|------|---------|-----------|---------|
| **todo.md** | Task board (WBS) | All 5 agents can edit | Process management: what to do, who does it, is it done |
| **journey.md** | Process log | All 5 agents can write | Audit retrospective: truthful record of task assignment and completion |
| **checklist.md** | Quality verification checklist | auditor generates in stage 8 | Result verification: done ≠ done right, verify item by item |

**The three files do not overlap:**
- todo = "do these things" (actionable tasks)
- journey = "how these things were done" (process log)
- checklist = "were they done right?" (quality gate)

#### todo.md — The Project Brain

**WBS Progressive Refinement:** Starting from MA's 12 stages, progressively refine into sub-tasks.

```
Stage level (initial, created by main):
  #4 coder: architecture design → 🔄
  #6 coder: coding implementation → ⬜

Sub-task level (agent self-refinement):
  #4 coder: architecture design → 🔄
    #4.1 data model design → ⬜
    #4.2 API interface design → ⬜
    #4.3 testable items checklist → ⬜
```

**Who Can Create Tasks:**

| Scenario | Task Creator | Assignee | Example |
|----------|-------------|----------|---------|
| main assigns work | main | Any agent | "#4 coder: architecture design" |
| Agent refines | Agent themselves | Self | "#4.1 coder: data model design" |
| Agent requests | Any agent | Other agent | "tester: supplement performance stress test" |

**Stage Completion Marker: All todo items for that stage are ✅.** Does not depend on time or feeling.

**Resume from Breakpoint:** Whose todo was the last completed + whose todo is the next to start → main continues directly.

**todo.md Format:**

```markdown
| # | Registration Time | Task Content | Responsible Agent | Status | Completion Time |
|---|------------------|-------------|-------------------|--------|-----------------|
| 0 | YYYY-MM-DD HH:MM | Draft project constitution (constitution.md) | main | 🔄 | - |
| 1 | YYYY-MM-DD HH:MM | Requirements analysis (spec+style-guide) | main | ⬜ | - |
| 2 | YYYY-MM-DD HH:MM | Pre-audit + clarify ambiguity identification | auditor | ⬜ | - |
| 3 | YYYY-MM-DD HH:MM | main gate check (review audit + delegate coder) | main | ⬜ | - |
| 4 | YYYY-MM-DD HH:MM | Architecture design (design.md) | coder | ⬜ | - |
| 5 | YYYY-MM-DD HH:MM | Cross-document consistency analysis (analyze) | main | ⬜ | - |
| 6 | YYYY-MM-DD HH:MM | Coding implementation | coder | ⬜ | - |
| 7 | YYYY-MM-DD HH:MM | Testing | tester | ⬜ | - |
| 8 | YYYY-MM-DD HH:MM | Delivery final audit + checklist quality checklist | auditor | ⬜ | - |
| 9 | YYYY-MM-DD HH:MM | main final confirmation + git merge + README draft | main | ⬜ | - |
| 10 | YYYY-MM-DD HH:MM | Documentation summary refinement + main review finalization | publicist+main | ⬜ | - |
| 11 | YYYY-MM-DD HH:MM | Post-hoc retrospective (triggered on demand) | auditor+main | ⬜ | - |
```

main creates todo.md at task startup, adds a row for each delegated task, and agents update status upon completion. Agents can self-refine sub-tasks.

**journey.md Format (mirrors todo):**

Each time a row in todo.md is completed, simultaneously append a record in journey.md:

```markdown
## YYYY-MM-DD

### HH:MM [main] Stage 0 constitution completed
- todo #0 ✅
- Core principles: [summary]

### HH:MM [auditor] Stage 2 pre-audit started
- todo #2 🔄

### HH:MM [auditor] Stage 2 pre-audit completed
- todo #2 ✅
- clarify found 3 ambiguous items, audit passed

### HH:MM [coder] Stage 4 architecture design started
- todo #4 🔄, reading constitution+spec
```

journey.md is the key basis for audit and retrospective — cross-reference with todo.md to check if the process is complete, if any steps were skipped, if any records are missing.

### 4.4 Pre-check: Projects Symlinks

**Before delegating any agent, main must confirm that projects symlinks are established.**

Agents have independent workspaces and cannot see the projects directory under main workspace.
If symlinks are not established, agents will report "project directory does not exist."

```bash
# Check if symlinks exist
for ws in workspace-auditor workspace-coder workspace-reviewer workspace-tester workspace-publicist; do
  target="$HOME/.openclaw/$ws/projects"
  if [ ! -e "$target" ]; then
    ln -s "$HOME/.openclaw/workspace/projects" "$target"
    echo "$ws: projects symlink created"
  else
    echo "$ws: projects OK"
  fi
done
```

### 4.5 Project Git and Version Management

The MA framework is the "subject" (`projects/ma/` independent repository), and the undertaking project is the "object" (each project has its own independent repository). The two are git-isolated and do not pollute each other.

#### 4.5.1 Git Initialization

```bash
cd projects/<project-name>
git init
echo -e "bin/\nout/\n*.class\n*.o\n*.so\n*.a\n*.log\n.DS_Store\n__pycache__/\n*.pyc\nnode_modules/\n.env\nbuild/\ntarget/\ndist/" > .gitignore
git add -A
git commit -m "init: project initialization (MA v$(cat ../ma/VERSION))"
```

> The `git init` and `.gitignore` creation from §4.1 are consolidated here; §4.1 only handles directory structure creation.

#### 4.5.2 Version Numbering

Semantic versioning `MAJOR.MINOR.PATCH`. The `VERSION` file in the root directory stores the current version.

| Level | Trigger | Example |
|-------|---------|---------|
| MAJOR | Architecture redesign, breaking changes | `1.0.0` → `2.0.0` |
| MINOR | New features, non-breaking changes | `1.0.0` → `1.1.0` |
| PATCH | Bug fixes, minor adjustments | `1.0.0` → `1.0.1` |

**Rules:**
- Project creation → `VERSION` = `0.1.0` (in development)
- First delivery + auditor approval → jump to `1.0.0`, create annotated tag
- main specifies the version number in stage 9; just inform coder/tester/auditor before switching
- tag: `git tag -a v1.0.0 -m "v1.0.0: summary"`, applied to the main branch

#### 4.5.3 Git Workflow

**Keep it simple: major versions merge to master, minor versions use one branch, hotfixes on the same branch serially.**

```
main branch
  └── feature/xxx → develop → self-test → tester verify → merge --no-ff → main → tag → delete branch

hotfix:
  Fix on the same feature branch, without creating a new branch. After fixing, tester verifies → continue merge flow.
```

- commit format: `type: description` (feat/fix/chore/docs/refactor/test)
- MA system is serial by default; no parallel development

#### 4.5.4 CHANGELOG.md

Maintained in the root directory; append one entry per delivery.

```markdown
# Changelog

## v1.0.0 — 2026-06-01 | MA 3.1.0
### Added
- Feature A
### Fixed
- Fix B
```

#### 4.5.5 Git Isolation

- `projects/` parent does not track subdirectories (only .gitignore)
- Each project `projects/<name>/` is an independent repository
- MA framework `projects/ma/` is an independent repository
- No mutual inclusion, no mutual reference


### main's Coach Decision Nodes

The coach doesn't intervene at every step but exercises judgment at key decision points:

| Decision Point | Trigger Timing | What the Coach Does |
|---------------|---------------|---------------------|
| **D0: Constitution drafting** | Project startup | Generate constitution.md based on template, define non-negotiable principles [NEW] |
| **D1: Requirement triage** | After user submits requirement | Judge complexity: simple → main handles / medium → coder+tester / complex → full workflow / writing → publicist |
| **D2: Audit review** | After auditor pre-audit completes | Review audit quality: anything missed? Standards rigorous enough? Pass → delegate to coder / Issues → supplement requirements |
| **D3: Design review** | After coder outputs architecture design | Review design reasonableness: over-engineering? Appropriate tech choices? Pass → enter analyze / Issues → return for revision |
| **D4: Consistency analysis** | After design completes, before coding begins | Check spec vs design vs constitution consistency [NEW: spec-kit] |
| **D5: Iteration decisions** | Auditor final audit discovers issues | Assess issue severity: P0-P1 must fix / P2 evaluate cost-benefit / P3 can be recorded as tech debt |
| **D6: Delivery judgment** | Auditor final audit passes | Confirm everything is ready: merge + README draft → delegate publicist → report to user |
| **D7: Progress intervention** | Agent unresponsive for over 30 minutes | sessions_send to query status, determine if stuck or still working |
| **D8: Coder implementation uncertainty** | Coder reports no examples reference, implementation approach uncertain | Consult tester (evaluate candidate approaches from testability/expected behavior perspective), summarize feedback to coder, record decision in design.md (stage 4) or journey.md (stage 6) |

**Coach's Three Principles:**
1. **Trust but verify.** Trust the agent's capabilities, but review key outputs every time
2. **Don't overstep.** The coach doesn't play on the field — coding is for coder, testing is for tester
3. **Sense of rhythm.** Don't rush or push; give agents enough time to think and execute, but follow up regularly

### Stage 0 Pre-stage — Complexity Assessment and Workflow Declaration [NEW: P1+P4]

**The first step of any project startup.** main performs complexity assessment before entering the full workflow.

**Input:** The user's original requirements

**Execution:**
1. Evaluate project complexity as S/M/L according to the criteria in §2.5
2. Declare the workflow stages to be executed (S-level 6/7 stages / M-level 9 stages / L full 11 stages)
3. List skipped items and rationale
4. Present assessment results and workflow declaration to the user; execute after user confirmation
5. Write the workflow declaration as the first entry in `docs/journey.md`
6. Initialize `docs/todo.md` based on the tailored workflow

**Workflow Declaration Template (first entry in journey.md):**

```markdown
## YYYY-MM-DD

### HH:MM [main] Complexity assessment + workflow declaration
- Complexity: S/M/L
- Assessment rationale: [brief description]
- Execution stages: [list stage numbers]
- Skipped items: [list skipped items and rationale]
- User confirmed ✅
```

**S-Level Special Handling:**
- constitution.md skipped (unless security is involved)
- Standalone design.md skipped, replaced by verbal/1-2 paragraph description
- analyze stage skipped, main directly checks consistency
- publicist writes README, main reviews
- auditor pre-audit can be replaced by main self-check

**M-Level Special Handling:**
- constitution.md decided on demand (keep when multi-person collaboration/security is involved)
- publicist reviews document readability after stage 1, unified polishing in stage 9
- Retrospective triggered on demand

**L-Level Special Handling:**
- Follow full 11 stages
- All deliverables complete
- publicist participates throughout

**Deliverables:** `docs/journey.md` (first entry) + `docs/todo.md` (initialized)

### Stage 0 — main Drafts Project Constitution (L-level default, S/M on demand) [NEW: spec-kit]

**Input:** User's original requirements + `projects/ma/main/constitution-template.md`

**Execution:**
1. Read constitution template `projects/ma/main/constitution-template.md`
2. Populate: project identity, core principles (3-5 verifiable principles), quality standards, technical constraints, non-goals, risks
3. Write to `docs/constitution.md`
4. The constitution defines the "non-negotiable bottom line" — all subsequent decisions (spec, design, audit, checklist) take this as the supreme reference
5. Update `docs/todo.md`, mark #0 as completed
6. **Write to `docs/journey.md`**: Record stage 0 completion time, constitution core principles summary

**Core Principle Writing Requirements:**
- Each must be verifiable — can clearly determine whether it is being followed
- Each must be non-vague — have specific violation examples
- Count: 3-5 items — more is unmemorable, equivalent to none

**Deliverable:** `docs/constitution.md`

### Stage 1 — main Requirements Analysis (Advisor Mode)

**Input:** User's original requirements

**Execution:**
1. Analyze requirements, decompose into executable functional points
2. Write `docs/spec.md` (goals, scope, acceptance criteria, non-functional requirements)
3. Write `docs/style-guide.md` (tech stack, naming conventions, formatting requirements)
4. Write `CONTRIBUTING.md` (team collaboration conventions)
5. Determine tech stack (desktop → Python, Web → JS/TS, Java projects as needed)
6. Update `docs/todo.md`, add "requirements pre-audit" task

**Deliverables:** spec.md + style-guide.md + CONTRIBUTING.md + initialized project directory

### Stage 2 — auditor Pre-audit + clarify Ambiguity Identification [ENHANCED]

**Trigger:** main notifies auditor via sessions_send

**Notification Content:**
```
Project: <project-name>
Task: Pre-audit + clarify ambiguity identification
Project path: projects/<project-name>/
Please review:
1. docs/constitution.md — Are constitution principles verifiable? [NEW]
2. docs/spec.md — Are requirements complete?
3. clarify ambiguity identification [NEW]:
   - Ambiguous words ("appropriate", "sufficient", "necessary", and other subjective judgment terms)
   - Unclear boundaries (input boundaries, exceptional inputs, extreme values)
   - Undefined behavior (what happens when user does something not described in spec?)
   - Contradictions (logical contradictions between two requirements?)
   - Missing scenarios (error paths, concurrency, resource exhaustion)
4. Constitution consistency [NEW]: Do spec requirements conflict with constitution core principles?
5. Security, testability, feasibility

After audit completes, reply directly to main with audit report attached at docs/audit-report.md.
```

**auditor Execution:**
- Read spec.md and style-guide.md
- Review item by item, record issues
- Write audit report to `docs/audit-report.md`
- **Write to `docs/journey.md`**: Record stage 2 completion time, audit conclusion, key findings
- Reply to main via sessions_send

### Stage 3 — main Gate Check

**After receiving auditor audit results:**
- Review audit quality
- Issues → supplement requirements → resubmit to auditor
- Pass → formally delegate to coder

### Stage 4 — coder Architecture Design + Testable Items Checklist [UPDATED]

**Trigger:** main sessions_send → coder

**Notification Content:**
```
Project: <project-name>
Project path: projects/<project-name>/
Task: Architecture design + generate testable items checklist

Please follow these steps:
0. [L-level] Read docs/constitution.md — understand project non-negotiable principles
1. Read docs/spec.md and docs/style-guide.md to understand requirements
2. Design architecture proposal (write to docs/design.md) — use design.md template
   - [L-level] Full template, all sections required
   - [M-level] Standard template, N/A sections may be omitted
   - [S-level] No standalone design.md, use 1-2 paragraphs to describe architecture
3. Generate testable items checklist [NEW]: List all verifiable functional points, boundary conditions, exceptional scenarios
4. Self-check spec ↔ design consistency (reference analyze check matrix) [NEW]
5. sessions_send → main with design.md + testable items checklist, wait for analyze confirmation before coding
6. Write to docs/journey.md
```

**coder Execution:**
- [S-level] Verbally describe architecture → generate testable items checklist → submit
- [M-level] Read spec → read `examples/` (if any) → design (standard template, omit N/A sections) → testable items checklist → self-check → submit
- [L-level] Read constitution → read spec → read `examples/` (if any) → design (full template) → testable items checklist → self-check → submit
- **Testable Items Checklist [NEW: User Decision]**: List all verifiable functional points, boundary conditions, and exceptional scenarios. tester uses this as the basis for test design and supplements any items coder missed.
- **Write to `docs/journey.md`**: Record stage 4 completion time, key design decisions, testable items count
- **Do not begin coding until main confirms analyze has passed**
- **Analyze Wait Fallback**: Wait >15 minutes with no response → sessions_send to query main [NEW]
- 🔴 **Escalation Path for Implementation Uncertainty**: coder reads `examples/` but is still uncertain about implementation approach → sessions_send → main, describing the uncertain points and considered candidate approaches → main consults tester (tester evaluates each candidate approach from testability and expected behavior perspectives) → main feeds tester's suggestions back to coder → coder forms final approach, records decision basis and tester suggestion summary in design.md

**Deliverables:** `docs/design.md` + testable items checklist

### design.md Template [NEW: P3]

> Design document template, supporting full mode (L-level) and minimal mode (S/M-level).
> Sections marked `[Required]` must be filled in.
> Sections marked `[S/M/L]` or with conditional markers are decided by project complexity.
> Inapplicable sections should be marked `N/A`.

```markdown
# Design: <project-name>

## 1. Architecture Overview [Required]

### 1.1 System Architecture Diagram
> Describe overall architecture using ASCII diagrams or text

### 1.2 Core Components
> List main components and their responsibilities

## 2. Data Model [If applicable: has data structures]

### 2.1 Entity/Model Definitions
> Define core data structures, models, entities

### 2.2 Data Flow
> How data flows between components

## 3. Interface Design [If applicable: has APIs/external interfaces]

### 3.1 Public Interfaces
> APIs, function signatures, command-line arguments

### 3.2 Internal Interfaces
> Inter-module communication protocols, callbacks, events

## 4. Module Detailed Design [Required]

### 4.1 Module A
> Responsibilities, core logic, key algorithms

### 4.2 Module B
> Same as above

## 5. Technology Selection and Rationale [L-level: Required / S/M: If applicable]

> Programming language, framework, library, build tool selection rationale

## 6. Error Handling Strategy [Required]

> Exception scenarios, error codes, logging strategy, degradation plans

## 7. Security Considerations [L-level: Required / M: If applicable / S: N/A]

> Permissions, sensitive data handling, authentication, input validation

## 8. Performance Considerations [L-level: Required / M: If applicable / S: N/A]

> Bottleneck assessment, optimization directions, resource budget

## 9. Testable Items Checklist [Required]

> Reference the testable items checklist generated by coder in stage 4

## 10. Change History

| Date | Version | Change | Author |
|------|---------|--------|--------|
| YYYY-MM-DD | 1.0 | Initial design | coder |
```

**Minimal Mode Usage Rules:**
- S-level: No standalone design.md, architecture description ≤ 2 paragraphs, written in journey.md or spec.md notes
- M-level: Use template, N/A sections written as "N/A". At minimum keep sections 1-4, 6, 9
- L-level: Full template, all applicable sections required

**Template vs Complexity Comparison Table:**

| Section | S-Level | M-Level | L-Level |
|---------|---------|---------|---------|
| 1. Architecture Overview | Verbal description | ✅ | ✅ |
| 2. Data Model | N/A | Fill if applicable | ✅ |
| 3. Interface Design | N/A | Fill if applicable | ✅ |
| 4. Module Detailed Design | Verbal description | ✅ | ✅ |
| 5. Technology Selection | N/A | Fill if applicable | ✅ |
| 6. Error Handling | Verbal description | ✅ | ✅ |
| 7. Security Considerations | N/A | Fill if applicable | ✅ |
| 8. Performance Considerations | N/A | N/A | Fill if applicable |
| 9. Testable Items | ✅ | ✅ | ✅ |
| 10. Change History | N/A | ✅ | ✅ |

### Stage 5 — analyze Cross-Document Consistency Check [NEW: spec-kit]

**Trigger:** After coder submits design.md, main performs consistency analysis

**Execution:**
1. Read `docs/constitution.md`, `docs/spec.md`, `docs/design.md`
2. Check item by item:

| Check Dimension | Specific Check |
|----------------|---------------|
| spec ↔ design | Does each spec requirement point have a corresponding design solution? Any omissions? |
| design ↔ constitution | Does the design violate constitution core principles? |
| spec ↔ constitution | Are there any requirements that conflict with the constitution? |
| design internal consistency | Do module interfaces match? Is data flow complete? |

3. When inconsistent:
   - Design issue → return to coder to modify design.md
   - Requirement issue → rollback to modify spec.md, re-run stages 2-3-4
   - Constitution conflict → confirm with user: change requirements or change constitution?
4. Analysis passed → notify coder to begin coding
5. Update `docs/todo.md`, mark #5 as completed
6. **Write to `docs/journey.md`**: Record stage 5 completion time, analysis conclusion

**Deliverable:** Analysis conclusion (recorded in journey.md; if passed, coder proceeds to stage 6 coding)
### Stage 6 — coder Coding Implementation [UPDATED: code only after analyze passes]

**Trigger:** main notifies coder "analyze passed, begin coding"

**Notification Content:**
```
Project: <project-name>
analyze consistency check has passed ✅
Please begin coding implementation:
1. Implement based on docs/design.md (write to src/)
2. Follow core principles in docs/constitution.md [NEW]
3. Self-test must pass
4. git add + git commit + git push origin feature/xxx
5. sessions_send → tester (with branch name and change summary)
6. sessions_send → main (with delivery summary)
7. Write to docs/journey.md
```

**coder Execution:**
1. Implement code (write to src/)
2. 🔴 **Implementation method uncertainty during coding**: First check `examples/` and existing design docs → still uncertain → sessions_send → main describing the issue (with candidate approaches) → main consults tester → tester gives suggestions from testability/expected behavior perspective → main feeds back to coder → coder continues. Decision process recorded in journey.md.
3. Self-test must pass
4. git add + git commit + git push origin feature/xxx
5. sessions_send → tester (with branch name and change summary)
6. sessions_send → main (with delivery summary)
7. **Write to `docs/journey.md`**: Record stage 6 completion time, code file list

Git conventions: branch off feature branch from main, commit after completion

### Stage 7 — tester Testing [ENHANCED: testable items checklist]

**Trigger:** coder sessions_send → tester

**tester Upon Receipt:**
1. `git fetch && git checkout feature/xxx` (confirm code is latest)
2. **Smoke test**: Confirm code builds/runs within 1 minute; if not, immediately return to coder
3. Read `docs/constitution.md` — understand non-negotiable principles, testing must cover constitution compliance items [NEW]
4. Read `docs/spec.md`, `docs/design.md`, **coder-generated testable items checklist** to understand requirements [UPDATED]
5. Design test cases (black-box + white-box), using testable items checklist as baseline, supplement missing items [UPDATED]
6. Execute tests, record results
7. If bugs → 🔴 **coder and tester may interact directly**, without going through main as relay each round, but must record in todo.md / journey.md
   - tester → coder: attach detailed bug report
   - coder fixes → tester regression verification
   - **Every iteration round must update todo.md (append sub-tasks) and journey.md** (record: bug description, root cause, fix approach, verification result)
   - **Iteration definition**: coder submits fix → tester completes verification → result recorded in journey.md counts as one round. New bugs introduced by the same fix don't count as new rounds; independent bugs newly discovered by tester count as new rounds
   - **Notify main both at first bug discovery and when all finally pass**
   - 🔴 **main acknowledgment**: Each round's todo.md records must have main's confirmation mark (e.g., `已阅-main` - reviewed by main) after being notified, ensuring main doesn't miss process information
   - 🔴 **Iteration cap: 3 rounds**. If unresolved issues remain after 3 rounds → main intervenes with ruling (① accept known defects recorded as tech debt ② expand design re-review ③ reassess requirements)
   - **Cap freeze**: tester continues testing other cases (non-blocking), pauses repeated verification of disputed bugs, awaits main ruling before deciding whether to add more tests
8. All pass → sessions_send → main (with test report, forwarded by main to auditor)
9. **Write to `docs/test-report.md`** (explicit step) [NEW]
10. **Update `docs/todo.md`**: Mark testing as completed
11. **Write to `docs/journey.md`**: Record stage 7 start/completion time, test result summary (case count/pass rate/bug count)

**Defect Report Closed-Loop Template:** Defects found during testing use `projects/ma/tester/test-report-template.md`, create `docs/bugs.md` from template, each defect goes through the four steps of "register→fix→verify→close." Iteration cap: 3 rounds.

### Stage 8 — auditor Delivery Final Audit + checklist Quality Checklist [ENHANCED: spec-kit + P2]

**Trigger:** tester sessions_send → main (with test report) → main sessions_spawn auditor

**auditor Execution:**
1. **Generate project-specific checklist** [ENHANCED: P2]:
   - Generate quality checklist based on spec + design + constitution + project complexity (S/M/L)
   - **Dynamic N/A item filtering:** Automatically skip checklist items unrelated to project characteristics
     - No database project → skip database-related items
     - S-level project → skip security audit, performance optimization, etc.
     - No external interface project → skip interface compatibility items
   - Write to `docs/checklist.md`
   - **Target scale:** S-level ~8-12 items / M-level ~15-20 items / L-level ~30-38 items
2. Trace item by item against pre-audit baseline
3. Review code changes (`git diff main...feature/xxx`)
4. Review process compliance (journey.md + todo.md)
5. Security audit (L-level: OWASP / M-level: basic security check / S-level: skip)
6. **README completeness check**: Compare `find` actual files with README project structure
7. **Check off each item in checklist.md**: Failed items marked `[FAIL]` with reason; items marked `[N/A]` indicate not applicable
8. Ruling:
   - Issues → sessions_send → main coach (with traceability report + checklist failed items)
   - Pass → sessions_send → main coach (with audit summary + checklist all ✅)
9. **Write to `docs/journey.md`**: Record stage 8 completion time, audit conclusion, checklist status

**Checklist Dynamic Filtering Rules [NEW: P2]:**
- auditor starts from the generic checklist template, judging each item:
  - ✅ Applicable to this project → keep
  - ❌ Not applicable to this project → mark as `[N/A]` with reason note
  - Example: No network functionality project skips "API rate limiting" item
- Final checklist only contains S/M/L-matched items
- Each kept item must have a clear judgment basis
- Inapplicable items marked `[N/A]` with reason, for retrospective reference

**Audit Issue Closed-Loop Template:** Issues found during audit use `projects/ma/auditor/audit-report-template.md`, create `docs/audit-issues.md` from template, each issue goes through the four steps of "register→track→verify→close." Closure ruling executed by main.

### Stage 9 — main Final Confirmation + README Draft [UPDATED: complexity-aware]

**After auditor final audit passes:**
1. Final confirmation `git diff main...feature/xxx`
2. **Version number**: Evaluate based on change scale (MAJOR/MINOR/PATCH), specify after informing coder/tester/auditor. Update VERSION → append CHANGELOG.md → `git tag -a v<version>` (detailed rules in §4.5)
3. `git merge --no-ff` to main, delete feature branch
5. **Generate README.md** — publicist writes based on project overview, main reviews and finalizes
4. Update `docs/todo.md`
5. [M/L-level] **Delegate to publicist**: sessions_send → publicist, with project context → publicist README → main review
6. **Write to `docs/journey.md`**: Record stage 9 completion time, merge operation

🔴 **Compiled Artifact Delivery Gate**:
- For projects requiring compilation (producing bin/apk/jar/so, etc.), **main must not deliver compiled artifacts to the user before tester testing is complete**
- Must wait for tester and coder to complete all iterations, all tests pass, and auditor final audit passes before main can deliver compiled artifacts to the user
- Violating this rule = red line. Allowing the user to receive untested compiled artifacts is unacceptable

### Stage 10 — publicist Summary Refinement + main Review Finalization [UPDATED: P5]

> **Complexity Notes:**
> - L-level: Independent stage, publicist participates throughout
> - M-level: Merged into stage 9, publicist participates in document polishing after stage 1
> - S-level: Skip this stage, main completes directly

#### publicist Throughout-Participation Guide [NEW: P5]

| Trigger Timing | L-Level Participation | M-Level Participation | S-Level |
|---------------|----------------------|----------------------|---------|
| Stage 1 spec completed | Review spec readability, structural clarity | Review spec readability | Skip |
| Stage 4 design completed | Review design doc readability, style consistency | Review design readability | Skip |
| Stage 6 coding begins | Begin drafting summary first draft | — | Skip |
| Stage 9 README | publicist writes README + writes summary | publicist writes README + writes summary | publicist writes README |
| Stage 10 final review | Refine all docs + main review finalization | Already included in stage 9 | Skip |

**L-Level Execution Flow:**

main may send context to publicist after stage 1:
1. Stage 1: `sessions_send → publicist`, with spec.md and style-guide.md, delegate readability review
2. Stage 4: `sessions_send → publicist`, with design.md, delegate structural clarity review
3. After stage 6: publicist begins drafting `docs/summary.md` first draft based on available information
4. Stage 9: main sends project context to publicist, publicist writes README
5. publicist's stages 4-5 feedback should converge within 2 rounds, not blocking the main flow

**Stage 10 Execution (L-level/M-level shared):**

After code merge, main delegates publicist to complete documentation:
1. main sessions_send → publicist, with project path and context
2. publicist reads code, journey.md, design.md, etc. to understand the project overview
3. publicist outputs:
   - **README.md refinement** — Summarize and refine based on main's draft, inject readability and polish
   - **Project summary** — Background, process, decisions, lessons learned (output to `docs/summary.md`)
4. publicist self-reviews three times before delivering to main
5. main reviews and confirms documentation accurately reflects technical content
6. **main Final Review Must-Check Items (check item by item, cannot skip):**
   - [ ] README project structure section matches `find` actual files exactly
   - [ ] Quick-start commands are executable (run through them)
   - [ ] Technical descriptions match code implementation
   - [ ] All links are valid
   - [ ] Layout and formatting meets standards
   - [ ] All quality standards listed in constitution.md are met [NEW]
   - [ ] All items in docs/checklist.md pass [NEW]
7. Notify user: project complete, documentation ready

**This is part of formal delivery and must not be omitted.** Code that runs is not deliverable without documentation.

### Stage 11 — Post-hoc Retrospective (auditor + main)

**Trigger:** Stage 10 completed, user or main initiates retrospective request.

#### Strict Subject/Object Separation 🔴

```
Retrospective subject → MA organization and agent processes (our own business, retrospective changes this)
Retrospective object → Completed development projects (historical records, don't touch them)
```

| Dimension | Subject (MA Organization Processes) | Object (Completed Development Projects) |
|-----------|-------------------------------------|----------------------------------------|
| **What it refers to** | team-dev SKILL.md, agent AGENTS.md/SOUL.md, workflows, red lines | Specific project code, docs/*.md, journey.md |
| **After retrospective** | ✅ Immediately modify process documents | ❌ Preserve the scene, no post-hoc modifications |
| **Why** | Processes are our "source code," bugs must be fixed | Projects are historical records; falsifying history is self-deception |
| **User requests project changes** | — | That's a **new round of development task**, follow full 0-10 workflow |

#### Retrospective Steps

The retrospective is organized according to the "Principles" five-step method (Goal→Problem→Diagnosis→Solution→Execution). See `projects/ma/docs/change-management.md §11.4` for details.

**Step 1: Review Goals (main)**
- What were the goals of this/this round of development? What were the original spec and CR?
- All roles align on original goals first, avoiding "hindsight bias" judgments

**Step 2: Present Facts (auditor reviews first)**
- Review the completed project's journey.md, compare against standard workflow, identify gaps and deviations
- Check audit-report.md completeness (pre-audit + final audit both recorded?)
- Check if there are recurring problem patterns in journey.md not yet fixed
- Output "process record audit report" to main

**Step 3: Diagnose Root Cause (auditor report distributed to agents + main summary)**

auditor report distributed to agents for confirmation (must execute, cannot skip):
- main sends audit report via sessions_spawn to coder, tester, publicist
- Each agent reads the audit report and, for findings relevant to themselves, returns a **Fact Confirmation Statement**:
  - **Fact Confirmation**: Did the finding in the audit actually happen? Honestly confirm or correct.
  - **Reason Explanation**: Why did it happen? Didn't know? Forgot? Or not required by the process?
  - **Improvement Suggestions**: How to prevent recurrence?
  - **Process Modification Suggestions**: If any, what specifically?
- Each agent's SOUL.md requires facing audit with honesty and facts:
  - Don't deny facts (right deviation)
  - Don't unprincipledly accept unfounded accusations (left deviation)
  - Don't quibble, deflect, or deceive

main summarizes diagnosis:
- Confirm auditor's findings
- Trace **three-layer root cause** (see change-management.md §11.2 Three Diagnostic Questions):
  1. Surface root cause: What was the direct cause? (Who did/didn't do what)
  2. System root cause: Which link in the process allowed this problem?
  3. Mechanism root cause: What mechanism in the MA framework should have caught this but didn't?
- Judgment: Is the problem a **process defect** or a **one-time execution error**?

**Step 4: Design Solution**
- **If process defect** (e.g., missing mandatory steps, incomplete checklist items) → modify change-management.md or skills/SKILL.md or projects/ma/<agent>/AGENTS.md
- **If execution error** (agent skipped steps, omissions) → record in journey.md, evaluate whether to strengthen red lines or add gates
- If the problem has occurred ≥ 2 times → must go through process-level fix, write to change-management.md

**Step 5: Execute and Verify**
- After modifications, sync agent workspaces
- Write to `projects/ma/docs/journey.md` (if applicable)
- Define verification criteria: Under the same scenario next time, this problem will not recur

#### Retrospective Outputs

| Output | Content | Action |
|--------|---------|--------|
| journey.md retrospective record | Problems, root causes, fixes, lessons | main writes |
| SKILL.md / AGENTS.md modifications | Process document reinforcement | main executes |
| Agent workspace sync | Templates → actual workspaces | main copies |
| *(Don't modify completed projects)* | *(Example Project A's journey.md kept as-is)* | — |

#### Retrospective Red Lines

- ❌ Post-hoc modification of completed project documents/code to "beautify" audit results
- ❌ Replacing "modify process documents" with "modify project files" — treating symptoms, not root cause
- ❌ Not recording retrospective findings in documentation
- ❌ Confusing subject and object — retrospective fixes organizational processes, not reworking completed projects

#### A New Round of Development ≠ Retrospective

If the user requests improvements on a completed project (e.g., completing journey.md, fixing bugs):
- This is a **new team development task**
- Follow the complete 0-10 stage workflow
- spec.md clearly states "what needs to change"
- Pre-audit, coding, testing, final audit — none can be skipped
- This is completely independent from the retrospective

## 6. Agent Communication and Scheduling

> See `projects/ma/multi-agent-design.md` §3 for the communication matrix.

### Scheduling Methods

- **sessions_send** — Send tasks to agents, wait for replies
- **docs/todo.md** — Shared task board, all agents can read/write
- **docs/journey.md** — Shared process records, all agents write key milestones
- **git** — Code changes in the `projects/<project-name>/` shared directory

### Status Tracking

main periodically tracks progress through:
1. Check `docs/todo.md` — task statuses
2. Check `docs/journey.md` — process records written by agents
3. Check git log — commit history

## 7. Git Workflow

```
main: git init → initialize project
    │
coder: 
    git checkout -b feature/xxx
    develop → git add && git commit
    sessions_send → tester
    │
tester:
    git checkout feature/xxx
    test → report
    sessions_send → main (with test report, forwarded by main to auditor)
    or sessions_send → coder (bug found)
    │
auditor:
    git diff main...feature/xxx
    git log main..feature/xxx
    audit → sessions_send → main
    │
main:
    git merge feature/xxx
    git branch -d feature/xxx
```

## 8. Progress Reporting Mechanism 🔔

### 8.1 main's (PM) Scheduled Feedback

**Frequency: At least once every 5 minutes.** main must continuously report progress to the user during team development.

**When there's progress — Standard Briefing:**
```
📊 [Project Name] [Complexity S/M/L] [Time]
Current Stage: [Stage Name] / [X] total stages
Task Status:
  - coder: [status] [brief]
  - tester: [status] [brief]
  - auditor: [status] [brief]
  - publicist: [status] [brief]
Next Step: [action]
```

> Stage list dynamically adjusts by complexity; displaying all stages is not required.
> Example: S-level current stage "3 coder: implementation / 6 total stages"
> Example: M-level current stage "5 main: analyze / 9 total stages"

**When there's no progress — One-Line Brief:**
```
📊 [Project Name] [Complexity S/M/L] [Time] — [Current Stage], [agent] executing, no anomalies.
```

**When there are risks/issues — Risk Escalation:**
```
⚠️ [Project Name] [Complexity S/M/L] Risk Alert
Risk: [Describe the risk]
Impact: [Quantified impact on progress/quality]
Actions Taken: [What we've done]
Alternative Plan: [Plan B]
Decision Needed: [If user needs to make a call, write it clearly]
```

### 8.2 Agent Work Completion Notification

**After each agent (coder / reviewer / tester / auditor / publicist) completes their work:**

1. agent sessions_send → main (with delivery summary)
2. **After main receives it, immediately send notification to the user**, including:
   - Which agent completed what task
   - What was delivered (files/reports/code change summary)
   - Who takes over next, doing what

**Prohibited Behaviors:**
- ❌ Agent silently hands off after completing tasks, user unaware
- ❌ main batches multiple agent completion notifications into one (send each agent's completion immediately)
- ❌ main goes over 5 minutes without feedback to user (even if no agent has completed, must send one-line brief)

## 9. Project Directory Specification (Following Open Source Conventions, Language-Agnostic)

Template structure (tailored by language conventions for specific projects):

```
projects/<project-name>/
├── docs/                    ← All documents centrally managed
│   ├── spec.md              ← main output, project specification
│   ├── design.md            ← coder output, architecture design
│   ├── style-guide.md       ← main output, code style conventions
│   ├── todo.md              ← main creates and maintains, task registration table
│   ├── test-report.md       ← tester output, test report
│   ├── audit-report.md      ← auditor output, audit report
│   └── journey.md           ← All agents write, process records
├── src/                     ← Source code (organized by language conventions)
│   ├── main/java/...        ← Java (Maven/Gradle convention)
│   ├── main.c, module.c...  ← C project flat layout
│   └── package_name/...     ← Python package
├── tests/                   ← Test files (Python/Go/JS)
│   └── (Java: optional, Maven project tests go in src/test/)
├── include/                 ← C/C++ header files (optional, needed for C projects)
├── scripts/                 ← Build/run/deploy scripts
├── bin/                     ← Build artifact directory (.gitignore, don't commit)
├── lib/                     ← Third-party dependencies/libraries (optional)
├── README.md                ← publicist output (last)
├── CHANGELOG.md             ← publicist/main maintains, version change records
├── CONTRIBUTING.md          ← main output, contribution guide
├── Makefile / pom.xml / CMakeLists.txt / pyproject.toml
└── .gitignore               ← bin/ out/ *.class *.log .DS_Store
```

**Language-Specific Tailoring Rules:**

| Language | src/ Convention | Test Location | Build File | bin/ lib/ |
|----------|----------------|---------------|------------|-----------|
| **Java** | `src/main/java/` + `src/main/resources/` | `src/test/java/` or `tests/` | pom.xml / build.gradle | bin/ (build artifacts, gitignore) |
| **C** | `src/*.c` flat or subdirectories | `tests/` | Makefile / CMakeLists.txt | bin/ (executables) + lib/ (.a/.so) + include/ (.h) |
| **Python** | `src/package_name/` or project root flat | `tests/` | pyproject.toml / setup.py | No bin/ lib/ needed |
| **Go** | Project root flat or `cmd/` + `pkg/` | Same file `_test.go` | go.mod | Not needed |
| **JS/TS** | `src/` | `tests/` or `__tests__/` | package.json | Not needed |

**Key Principles:**
- `docs/`, `README.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, `.gitignore` are standard for all projects
- Subdirectories under `src/` follow the chosen language's community conventions, determined by coder during architecture design
- `bin/`, `lib/`, `include/` created as needed by language
- `tests/` as general test directory; Java projects may use `src/test/` instead

## 10. Sub-Agent Assumptions and Dependencies

This skill assumes the following agents are configured in OpenClaw (see `projects/ma/multi-agent-design.md` section 5):

- `coder` — workspace: `~/.openclaw/workspace-coder`, profile: coding
- `tester` — workspace: `~/.openclaw/workspace-tester`, profile: coding
- `auditor` — workspace: `~/.openclaw/workspace-auditor`, profile: coding
- `publicist` — workspace: `~/.openclaw/workspace-publicist`, profile: coding

Each agent's SOUL.md and AGENTS.md are at `projects/ma/<agent>/`.

**If agent is not configured:** main should fall back to single-agent mode, completing all roles independently (following AGENTS.md rules for design→code→test→audit full workflow). After task completion, inform the user "agent not configured, main completed single-handedly."

## 11. Change Management

> See `projects/ma/docs/change-management.md` for complete change management workflow specifications

Change management is a **reinforcement layer** on top of the standard team development workflow — adding formalized, traceable change control mechanisms on top of the existing S/M/L standard workflow.

### 11.1 Core Change Management Rules

The MA framework incorporates the following change management rules; all roles must follow them:

| # | Rule | Related Section |
|---|------|----------------|
| R1 | **Every change must be registered with a CR.** No CR means no development entry | change-management.md §2/§3 |
| R2 | **Complete impact analysis self-check list before changes.** Any high-risk item hit must be elaborated in design.md | change-management.md §4 |
| R3 | **Core files (>500 lines) changes go through coder; main does not directly edit.** Violation is a red line | change-management.md §5 |
| R4 | **Cross-module data transformations must provide input→output examples (≥2 sets) in design.md.** reviewer verifies each set | change-management.md §6 |
| R5 | **Non-blocking issues must be recorded in todo.md pending fix list.** No verbal agreement to "fix next time" | change-management.md §7 |
| R6 | **Agents must have fallback models; auto-switch when primary model unavailable.** Don't silently continue when unavailable | change-management.md §9 |
| R7 | **Schedule at least one coder↔reviewer role swap every 3 Sprints.** Break mental patterns | change-management.md §8 |
| R8 | **Pain + reflection = progress.** Trigger root cause diagnosis for every problem/violation; tolerate no repeat of the same mistake | change-management.md §11 |

### 11.2 Change Process Integration

Integration of change management with standard team development workflow:

```
User proposes change
    │
    ▼
1. main evaluates change type and scope ──→ see change-management.md §10
    │  ├── New feature → standard M/L workflow
    │  ├── Bug fix → standard S/M workflow
    │  └── In-development requirement change → delta change workflow below
    │
    ▼
2. CR Registration
    │  Record to docs/CR.md (simplified or full version based on change type)
    │
    ▼
3. Impact Analysis Self-Check List
    │  coder completes → main reviews
    │  (S-level can verbalize; M/L-level must write to document)
    │
    ▼
4. Enter standard team development workflow (corresponding S/M/L level)
    │  Notes:
    │  - design.md appends change description, does not overwrite original design
    │  - commit notes CR number
    │  - Regression testing ensures no breakage
    │  - Non-blocking issues recorded in todo.md
    │
    ▼
5. Closure
    │  CHANGELOG notes CR number
    │  Update docs/CR.md, mark as closed
    │
    ▼
Complete
```

## 12. Exception Handling

### 12.1 Problem Diagnosis Process (problem-solving-methodology)

**Trigger Conditions**: When any problem requiring investigation arises during development (auditor discovers issues, tester finds bugs, coder encounters technical obstacles, iteration fix failures, etc.), all agents must apply structured diagnosis; guess-and-fix is prohibited.

**Core Process** (see `skills/problem-solving-methodology/SKILL.md` for details):

```
Step 0: Dissolution Layer — Is the problem itself valid? (language traps/hypothesis testing/problem classification)
  ↓
Step 1: Define Problem — Phenomenon vs Expected vs Impact (no assumptions)
  ↓
Step 2: Diagnose — Map call chain → stepwise verification → narrow scope → confirm root cause
         Three Root Cause Questions: Why? (mechanism explanation) / Sufficient? (sufficiency) / Unique? (exclusivity)
  ↓
Step 3: Design Solution — At least 2 candidate solutions, compare effectiveness/risk/complexity/reversibility
  ↓
Step 4: Execute — Change one variable at a time, record original value
  ↓
Step 5: Verify — Direct/Regression/Persistence triple verification
  ↓
Step 6: Retrospect — Root cause in one sentence + detours + lessons
```

**Anti-Stall Mechanisms**:
- Same hypothesis verified 3 consecutive times with no conclusion → change hypothesis
- Cumulative diagnosis >15 minutes with no progress → pause, report excluded items
- Cumulative >5 hypotheses all negated → consider dissolution (return to Step 0)
- Problem recurs after fix → don't stack patches, rollback and re-diagnose

**Anti-Patterns (Prohibited for All Agents)**:

| Anti-Pattern | Manifestation | Correct Approach |
|-------------|--------------|-----------------|
| Guess-and-fix | See symptom → directly change | First map the call chain |
| Surface fix | Change wrong value, don't ask why this value | Ask "Why did it become this value?" |
| Multi-change | Change 3 places at once | One variable at a time |
| Premature victory | "Should be fine" without evidence | Show verification results |
| No rollback | Forget to record original value | Backup before changing |

### 12.2 Standard Exception Handling

| Exception | Handling |
|-----------|----------|
| auditor unresponsive >30 min | main re-trigger audit or inform user |
| coder/tester stuck | main proactively sessions_send to query status |
| agent model error/timeout | main retry once; if still fails, inform user |
| Requirements change mid-development | **Follow full requirement change process (see below)** |
| Audit failure cyclic iteration | After 3 rounds, main reports to user and requests decision |

### Requirement Change Handling Process 🔴

**Core Principle: Maximum inheritance, append modifications, don't overwrite.**

```
Existing deliverables (spec.md / code / tests) are not deleted or rewritten.
Changes are incremental — append and modify on top of the original, preserving complete evolution history.
```

#### Change Process

```
User proposes change
    │
    ▼
1 main appends to spec.md (adds new "Requirement Change Record" section, does not overwrite original requirements)
    │
    ▼
2 auditor delta pre-audit (only audit changed parts, compare with original requirements, check conflicts)
    │
    ▼
3 main determines impact scope
    ├── Affects existing code → enter change development process (4-5-6)
    └── Does not affect existing code → continue original process
    │
    ▼
4 coder appends modifications (does not rewrite existing code, appends new features or modifies affected parts)
    │
    ▼
5 tester delta testing (preserve all original test cases, append change-related cases)
    │
    ▼
6 auditor delta final audit (trace against change baseline, confirm no regression breakage)
    │
    ▼
7 main merge → publicist supplements documentation → main review → notify user
```

#### spec.md Change Record Format (append, don't overwrite)

```markdown
---

## Requirement Change Records

### Change #1 — YYYY-MM-DD HH:MM
**Changer:** User
**Change Summary:** [One-line description]
**Change Details:**
- Original Requirement: [Quote relevant sections of original requirements]
- New Requirement: [Describe after change]
**Impact Scope:**
- Code: [Which parts of existing code are affected]
- Tests: [Which test cases need adjustment or addition]
- Documentation: [Which sections of README/design docs need updating]
**Related:** See audit-report.md Change #X audit
```

#### Requirements for Each Role in Requirement Changes

| Role | Responsibilities in Change | Prohibited Behaviors |
|------|--------------------------|---------------------|
| **main** | Append to spec.md (don't rewrite original requirements); determine impact scope; notify all relevant agents | ❌ Delete or rewrite original requirements ❌ Skip delta audit |
| **auditor** | Delta audit — only audit changed parts; check conflicts and contradictions with original requirements; confirm no regression breakage | ❌ Treat as brand new full audit ❌ Ignore inheritance relationship between change and original requirements |
| **coder** | Append modifications on existing code; git checkout without deleting existing functionality; design.md appends change description | ❌ Rewrite entire file ❌ Delete original functionality unless explicitly requested |
| **tester** | Preserve all original test cases; append change-related test cases; delta regression to ensure no breakage | ❌ Delete original test cases ❌ Only test new functionality without regression testing |
| **publicist** | README appends change description; summary.md records change process | ❌ Rewrite entire document |

#### Change Recording in journey.md

```markdown
| YYYY-MM-DD HH:MM | Requirement Change #1 | User proposed change: [summary]. Impact: [scope judgment]. Entering change process |
| YYYY-MM-DD HH:MM | Change Audit Completed | Auditor delta audit passed. Change does not affect existing code / affects X locations |
```

#### Change Example: Append vs Overwrite

| Action | ❌ Overwrite (Prohibited) | ✅ Append Modification (Correct) |
|--------|--------------------------|--------------------------------|
| spec.md | Change original requirement "rabbit jumps over fox" to "rabbit jumps over hedgehog" | Keep original requirement; add Change Record #1: character changed from fox to hedgehog |
| Code | Delete fox's CSS, rewrite hedgehog's CSS | Add new `.hedgehog` CSS; change `.fox` to `display:none` (preserve original code) |
| Tests | Delete fox test cases, write entirely new set | Keep original test cases (marked "original requirement"); add new change test case group |
| README | Full text replacement | Keep project overview; add new "Requirement Change Records" subsection |
| design.md | Overwrite architecture design | Keep original plan; add new "## Change #1 Architecture Adjustment" subsection |

---

## 12.5 Common Issues and Troubleshooting

> Reference the troubleshooting methodology from "How to Write Good Skills."

### Q1: AI didn't trigger this Skill, responded with general knowledge instead

**Troubleshooting Steps:**
```
Skill not triggered
    ↓
1. Did the user say "团队研发" or "多agent开发"? — No → Re-ask with trigger keyword
    ↓ Yes
2. Is the Skill loaded in the current session? — No → Check SKILL.md path and YAML metadata format
    ↓ Loaded
3. Is main engaged in another task? — Yes → Wait for current task to complete, or use /new to start a new session
    ↓ No
4. Directly say "团队研发:..." to trigger
```

### Q2: main started but agent is not responding

```
Agent not responding
    ↓
1. Check agent workspace projects symlink: `ls -la ~/.openclaw/<agent>/projects`
    ↓ OK
2. Check if agent is reachable: send a test message via `sessions_send → <agent>`
    ↓ Unreachable
3. Confirm agent model availability (balance, service status)
4. Fallback: switch to alternate model and re-spawn
```

### Q3: Coder's design deviates from requirements

**Common Causes:**
- spec.md written unclearly (ambiguity not resolved in stage 2 clarify)
- constitution.md not read by coder (assumed already embedded in stage 4 design, but newly joined coder may miss it)

**Handling:** main discovers inconsistency during stage 5 analyze → return to coder to modify design → reviewer re-reviews.

### Q4: Tester finds severe bug with broad impact

**Handling Process:**
1. tester directly informs coder (via stage 7 direct interaction)
2. coder evaluates fix scope, updates journey.md
3. After fix, tester performs full regression (not just verifying that bug)
4. If fix involves core files, reviewer re-reviews
5. If iterations exceed 3 rounds, main intervenes with ruling

### Q5: CR process was skipped, code was changed directly

**Root Cause Analysis:**
- If one-time oversight → retroactively register CR, update journey.md record
- If it happens again → CR process constraints are insufficient → add gate in CR.md: commits without CR numbers are not merged

### Q6: Retrospective findings not closed out

**Cause:** Retrospective outputs not converted into trackable action items.

**Fix:** After retrospective, must produce clear action items (who + what + deadline), written to journey.md, todo.md, and change-management.md (if applicable). At the start of the next round, main checks whether retrospective action items are completed.

---

## 13. Red Lines

- **Follow compliant process** — Execute the tailored workflow by complexity S/M/L; skipping necessary steps within the rules is not allowed
  - [L-level] Auditor pre-audit + delivery final audit must not be skipped
  - [M-level] Auditor pre-audit + delivery final audit (may be combined or lightweight)
  - [S-level] main self-check replaces independent auditor pre-audit, but final audit still requires auditor
- **Only report completion after auditor passes** — Testing passed ≠ deliverable
- **main does not overstep** — Coding is for coder, testing is for tester, auditing is for auditor
- **All outputs written to projects/** — Git-managed, nothing lost
- **Failure must explain cause** — If any agent has problems, explain cause and current status to user
- **🔴 Every role must notify user upon work completion** — Agent completes → notifies main → main immediately notifies user. Silent handoffs prohibited.
- **🔴 main reports progress to user at least every 5 minutes** — Even with no changes, send status update. Don't keep user waiting.
- 🔴 **todo.md and journey.md are the project constitution** — They span all 12 stages. Modification, deletion, or weakening requires user's highest-level consent. No one (including main) may operate arbitrarily.
- 🔴 **Stage completion marker = all todo items for that stage are ✅** — Not based on feeling, not based on time, only based on todo.
- 🔴 **Compiled artifact delivery gate** — For projects requiring compilation, main must not deliver bin/apk/jar or other compiled artifacts to user before tester+coder iterations complete, all tests pass, and auditor final audit passes.
- 🔴 **Coder-tester direct interaction must leave traces** — In stage 7, coder and tester may interact directly for efficiency, but each round must update todo.md and journey.md; first bug discovery and final all-clear must notify main. Iteration cap: 3 rounds.
- 🔴 **Coder must escalate when uncertain about implementation** — When stage 4/6 finds no examples reference, implementation approach uncertain, coder does not decide independently. Escalation path: coder→main→tester (testability assessment)→main→coder, decision basis recorded in design.md (stage 4) or journey.md (stage 6).

---

### Change Management Specific Red Lines 🔴

> See `projects/ma/docs/change-management.md` for details

- 🔴 **Every change must register a CR** — No CR means no development entry. Even a 1-line bugfix requires CR registration (simplified version). See change-management.md §3.
- 🔴 **Impact analysis must be performed before changes** — coder completes self-check list, main reviews. S-level can verbalize; M/L-level must write to document. See change-management.md §4.
- 🔴 **Core files (>500 lines) main must not directly edit** — Core file modifications must go through coder. main only makes decisions and reviews. Edit tool scope: config files, test files, documentation, small utility scripts (<500 lines). See change-management.md §5.
- 🔴 **Cross-module data transformations must provide input→output examples (≥2 sets) in design.md** — Provide to reviewer for review before coding implementation; only code after review passes. See change-management.md §6.
- 🔴 **Non-blocking issues must not be verbally agreed as "fix next time"** — Non-blocking issues found by reviewer/tester must be recorded in todo.md pending fix list; main arranges fix plan. See change-management.md §7.
- 🔴 **Tester must fallback or escalate when primary model is unavailable** — Auto-use fallback when primary model fails; if fallback also fails, inform user. Silent continuation is prohibited. See change-management.md §9.

---

## 14. Loop Engineering 集成（v2.0）

基于 debugloop 项目 45 轮迭代实战验证。

### 五大构建块

| 构建块 | 通用做法 |
|--------|---------|
| Goal（目标） | 量化为可自动检测的指标 |
| Generate（生成） | LLM 生成候选方案 |
| Execute（执行） | 确定性工具自动化 |
| Verify（验证） | 用指标变化替代 True/False |
| Decide（决策） | 多维度阈值（停滞/超限/优先级） |

### 核心模式

1. **过滤优于修复** — 无法覆盖的模块直接过滤
2. **真文件优于 mock** — tempfile 比 unittest.mock 更可靠
3. **人 find pattern, LLM implement** — 瓶颈时切换为人分析
4. **累积制** — 通过产物永久保留，覆盖率自然爬升
5. **前置审计** — 设计阶段发现问题的成本是编码阶段的 1/5

### 角色分工红线

- 测试是 tester 职责，不是 coder 的
- auditor 只发现，tester 解决
- Reviewer 必须独立参与
- Main 每轮后查余额
