# todo-template.md — Project Task Board Template

> Copy this template to `projects/<ProjectName>/docs/todo.md`.
> **Phase completion marker: All todo items in a phase must be checked ✅.**
> Modifying/deleting/weakening any item requires top-level user approval. All 5 agents may edit.

---

## Project Info

| Field | Value |
|------|-----|
| Project Name | `<ProjectName>` |
| Complexity | S / M / L |
| Process Phase Count | `<N>` |
| Start Time | `YYYY-MM-DD HH:mm` |

---

## WBS Progressive Elaboration

todo.md expands from coarse to fine incrementally — no need to fill everything in from the start:

```
Phase-level (initial, created by main):
  #4 coder: Architecture Design → 🔄
  #6 coder: Coding Implementation → ⬜

Subtask-level (agents elaborate on their own):
  #4 coder: Architecture Design → 🔄
    #4.1 Data Model Design → ⬜
    #4.2 API Interface Design → ⬜
    #4.3 Testable Items Checklist → ⬜
  #6 coder: Coding Implementation → ⬜
```

---

## Who Can Create Tasks

| Scenario | Creator | Assignee | Example |
|------|---------|--------|------|
| main dispatches work | main | any agent | `#4 coder: Architecture Design` |
| agent elaborates | agent themselves | self | `#4.1 coder: Data Model Design` |
| agent requests | agent | another agent | `#6.1 tester: Performance Stress Test` |

---

## Closed-Loop Subtask Templates

> When agents enter the following phases, expand the corresponding template into subtasks to track the complete closed loop.

### 🔧 Coder Self-Test Closed Loop (Phase 6)

```
#6 coder: Coding Implementation → 🔄
  #6.1 Coding → ⬜
  #6.2 Self-Test Plan (cross-reference #4 Testable Items Checklist) → ⬜
  #6.3 Self-Test Execution → ⬜
  #6.4 Bug Fix Iteration (direct ✅ if no issues) → ⬜
    #6.4.1 [Issue Summary] → ⬜
      ├ Root Cause: [analysis] → ⬜
      ├ Fix: [solution] → ⬜
      ├ Self-Test Regression → ⬜
  #6.5 Self-Test All-Pass Gate → ⬜
  #6.6 Write to docs/journey.md → ⬜
  #6.7 git commit + git push origin feature/xxx → ⬜
  #6.8 sessions_send → tester (attach branch name + change summary) + main (attach delivery summary) → ⬜
```

**Rules:**
1. Self-test plan must cross-reference the testable items checklist produced in #4 — verify each item
2. Fast path for no issues: #6.3 zero findings → #6.4 directly ✅
3. Append subtasks for each issue found, each expanded into 3 steps: Root Cause → Fix → Regression. Multiple issues append #6.4.2, #6.4.3…
4. #6.5 is a hard gate: proceed only after all items are ✅; do not advance to subsequent steps otherwise
5. Must git push (tester needs to pull the code)
6. Notify both tester + main simultaneously — no omission

### 🧪 Tester Defect Closed Loop (Phase 7)

```
#7 tester: Testing → 🔄
  #7.1 Accept Delivery & Confirm Requirements → ⬜
    #7.1.1 Switch branch, confirm code is up to date → ⬜
    #7.1.2 Read constitution + spec + design + testable items checklist → ⬜
  #7.2 Smoke Test → ⬜
  #7.3 Black-Box Test Case Design → ⬜
  #7.4 White-Box Test Case Design → ⬜
  #7.5 Test Execution → ⬜
    #7.5.1 Functional Path Coverage → ⬜
    #7.5.2 Boundary/Exception Path Coverage → ⬜
    #7.5.3 Verify Against Spec Item by Item → ⬜
  #7.6 Defect Management (Iteration Round 1) → ⬜
    #7.6.1 [BUG-001] Register → ⬜
    #7.6.2 [BUG-001] Report to coder + sync to main → ⬜
    #7.6.3 [BUG-001] coder fix → ⬜
    #7.6.4 [BUG-001] Regression Test (full scope) → ⬜
    #7.6.5 [BUG-001] Verify → ⬜
    #7.6.6 [BUG-001] Close / Reopen → ⬜
  #7.7 Defect Management (Iteration Round 2, if any) → ⬜
  #7.8 Defect Management (Iteration Round 3, if any) → ⬜
  #7.9 Final Regression Test → ⬜
  #7.10 Test Report & Delivery → ⬜
    #7.10.1 Write to docs/test-report.md → ⬜
    #7.10.2 Write to docs/journey.md → ⬜
    #7.10.3 Align with auditor checklist → ⬜
    #7.10.4 sessions_send → main (attach branch / case count / coverage / pass rate) → ⬜
```

**Rules:**
1. Smoke test failure → immediately return to coder, sync main
2. Each bug reported to coder and synced to main — no batching
3. Full regression after each round of fixes, not just verifying that bug
4. Iteration cap: 3 rounds; main intervenes if exceeded
5. Final delivery must include test-report.md + journey.md
6. If auditor checklist exists, test coverage must align

### 🔒 Auditor Issue Closed Loop (Phase 2: Pre-Audit)

```
#2 auditor: Pre-Audit → 🔄
  #2.1 Clarify — Ambiguity Identification → ⬜
    #2.1.1 Subjective Term Quantification Check → ⬜
    #2.1.2 Boundary/Exception/Edge Value Definition → ⬜
    #2.1.3 Undefined Behavior Check → ⬜
    #2.1.4 Requirement Contradiction Check → ⬜
    #2.1.5 Missing Scenario Check → ⬜
    #2.1.6 Acceptance Criteria Testability → ⬜
  #2.2 Completeness Check → ⬜
  #2.3 Constitution Consistency Check (cross-reference constitution.md) → ⬜
  #2.4 Security Check → ⬜
  #2.5 Testability + Feasibility Check → ⬜
  #2.6 Issue Management → ⬜
    #2.6.1 [AUDIT-001] Register → ⬜
    #2.6.2 [AUDIT-001] Report to main → ⬜
    #2.6.3 [AUDIT-001] main decides → ⬜
    #2.6.4 [AUDIT-001] main corrects/supplements → ⬜
    #2.6.5 [AUDIT-001] auditor rechecks → ⬜
    #2.6.6 [AUDIT-001] main adjudicates closure → ⬜
  #2.7 Audit Report → ⬜
    #2.7.1 Write to docs/audit-report.md → ⬜
    #2.7.2 Write to docs/journey.md → ⬜
    #2.7.3 sessions_send → main (attach audit summary + findings count) → ⬜
```

**Pre-Audit Rules:**
1. All 6 clarify items must be completed; do not pass with any unresolved items
2. Issue management does not involve coder/tester (Phase 2: main corrects requirements)
3. main may dispatch to coder only after passing

### 🔒 Auditor Issue Closed Loop (Phase 8: Final Audit)

```
#8 auditor: Final Audit → 🔄
  #8.1 Checklist Generation → ⬜
    #8.1.1 Generate docs/checklist.md based on spec+design+constitution → ⬜
  #8.2 Requirement Traceability (cross-check against Phase 2 pre-audit baseline item by item) → ⬜
  #8.3 Code Change Review (git diff main...feature/xxx) → ⬜
  #8.4 Process Compliance Review (journey.md + todo.md) → ⬜
  #8.5 README Completeness Check → ⬜
  #8.6 Security Audit (OWASP Top 10) → ⬜
  #8.7 Checklist Item-by-Item Verification → ⬜
  #8.8 Issue Management → ⬜
    #8.8.1 [AUDIT-001] Register → ⬜
    #8.8.2 [AUDIT-001] Report to main → ⬜
    #8.8.3 [AUDIT-001] main decides (fix/ignore/technical debt) → ⬜
    #8.8.4 [AUDIT-001] coder fix → ⬜
    #8.8.5 [AUDIT-001] tester verify → ⬜
    #8.8.6 [AUDIT-001] auditor recheck → ⬜
    #8.8.7 [AUDIT-001] main adjudicates closure → ⬜
  #8.9 Audit Report → ⬜
    #8.9.1 Write to docs/audit-report.md → ⬜
    #8.9.2 Write to docs/journey.md → ⬜
    #8.9.3 sessions_send → main (attach audit summary + checklist status) → ⬜
```

**Final Audit Rules:**
1. Checklist must be generated before verification; do not begin final audit without it
2. Do not pass without 100% checklist completion
3. Requirement traceability must cross-reference Phase 2 pre-audit baseline
4. Issues follow 7-step process: Register → Decide → Fix → Verify → Recheck → Close
5. Closure authority rests with main (auditor only discovers, does not adjudicate)

---

## Task List

| # | Log Time | Task Description | Assignee Agent | Status | Completion Time | Notes |
|---|---------|---------|-----------|------|---------|------|
| 0 | | Complexity Assessment + Process Declaration | main | ⬜ | | |
| 0b | | Draft Project Constitution | main | ⬜ | | Required for L-level |
| 1 | | Write spec.md + style-guide.md | main | ⬜ | | |
| 2 | | Pre-Audit + Clarify Ambiguity Identification | auditor | ⬜ | | |
| 3 | | Audit Quality Gate, Dispatch to coder | main | ⬜ | | |
| 4 | | Architecture Design + Testable Items Checklist | coder | ⬜ | | |
| 5 | | Analyze — Cross-Document Consistency Check | main | ⬜ | | |
| 6 | | Coding Implementation | coder | ⬜ | | |
| 7 | | Testing | tester | ⬜ | | |
| 8 | | Final Audit + Checklist | auditor | ⬜ | | |
| 9 | | Merge + README Initial Draft | main | ⬜ | | |
| 10 | | Documentation Summary & Refinement | publicist | ⬜ | | L-level |
| 11 | | Post-Mortem Review | auditor+main | ⬜ | | On demand |

---

## Status Legend

| Status | Meaning |
|------|------|
| ⬜ | Not Started |
| 🔄 | In Progress |
| ✅ | Completed |
| ❌ | Canceled |
| ⚠️ | Blocked / At Risk |
| 🔁 | Returned for Rework |

---

## Breakpoint Resume

When a project is interrupted, todo.md is the sole recovery reference:

```
Last completed todo: #5 analyze ✅
Next pending todo: #6 Coding Implementation ⬜ (assignee: coder)
→ main directly messages coder: "Continue Project X, Phase 6 coding, branch feature/xxx"
```
