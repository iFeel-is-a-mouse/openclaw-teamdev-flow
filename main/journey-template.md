# journey-template.md — Project Process Log Template

> Copy this template to `projects/<ProjectName>/docs/journey.md`.
> **Mirror of todo.md — for every line completed in todo.md, append a matching record in journey.md.**
> Critical basis for audits and post-mortems. All 5 agents may write. Modifying/deleting/weakening requires top-level user approval.

---

## Entry #1: Process Declaration

```markdown
## [YYYY-MM-DD HH:mm] Phase 0 Complete: Complexity Assessment & Process Declaration

📋 Complexity Assessment: [S/M/L] (Criteria: [summary])
📋 Execution Process: [list phases to execute]
📋 Skipped/Merged: [list skipped phases and reasons]
📋 Constitution Core Principles: [3-5 verifiable principles] (L-level)

User Confirmed: ✅
```

---

## Daily Log Format (Mirroring todo.md)

For each completed todo.md task line, append one record:

```markdown
## YYYY-MM-DD

### HH:mm [agent] Phase N [Phase Name] Started
- todo #N 🔄

### HH:mm [agent] Phase N [Phase Name] Completed
- todo #N ✅
- Key Deliverables: [list of outputs]
- Key Decisions: [decisions and rationale]
```

**Full Example:**

```markdown
## 2026-05-24

### 14:30 [main] Phase 0 Complexity Assessment Started
- todo #0 🔄
- Analyzing requirements, assessed as M-level (200-1000 lines, 4-10 files, 2-3 modules)

### 14:35 [main] Phase 0 Completed
- todo #0 ✅
- Process declaration: 0→1→2→3→4→5→6→7→8→9 (9 phases)
- Skipped: constitution.md (M-level on demand), publicist standalone phase (merged into #9)

### 14:40 [main] Phase 1 Requirements Analysis Started
- todo #1 🔄

### 15:20 [main] Phase 1 Completed
- todo #1 ✅
- Deliverables: docs/spec.md, docs/style-guide.md

### 15:22 [main] Phase 2 Pre-Audit Started
- todo #2 🔄
- sessions_send → auditor: Pre-audit

### 15:30 [auditor] Phase 2 Completed
- todo #2 ✅
- Clarify found 3 ambiguity items, see audit-report.md for details
- Constitution consistency: Passed

### 15:32 [main] Phase 3 Quality Gate Completed
- todo #3 ✅
- Reviewed audit report, quality approved, dispatching to coder

### 15:35 [coder] Phase 4 Architecture Design Started
- todo #4 🔄
- Branch: feature/user-auth

### 16:10 [coder] Phase 4 Completed
- todo #4 ✅
- Deliverables: docs/design.md + Testable Items Checklist (12 items)
- Key Decision: Use JWT + Redis approach, drop Session approach (stateless requirement)
- Delivered to main

### 16:12 [main] Phase 5 Analyze Completed
- todo #5 ✅
- spec ↔ design ↔ constitution consistency check passed

### 16:15 [coder] Phase 6 Coding Started
- todo #6 🔄

### 17:00 [coder] Phase 6 Completed
- todo #6 ✅
- Code files: UserAuth.java, AuthService.java, AuthController.java
- commit: a1b2c3d
- sessions_send → tester: Code ready, branch feature/user-auth

### 17:05 [tester] Phase 7 Testing Started
- todo #7 🔄
- Smoke test passed, test cases designed (8 black-box + 4 white-box = 12 items)

### 17:20 [tester] Bug Found — Iteration Round 1
- BUG-001: NPE when password is empty, severity P1
- sessions_send → coder: BUG-001 details

### 17:25 [coder] BUG-001 Fixed
- Root cause: AuthService.validate() missing null check
- Fix: Added @NonNull validation, commit b2c3d4e
- sessions_send → tester: Fixed

### 17:30 [tester] BUG-001 Verified
- Original test case recheck ✅
- Regression test 4 items ✅, no new defects found
- Iteration Round 1 closed

### 17:35 [tester] Phase 7 Completed
- todo #7 ✅
- Test results: 12 cases / 12 passed / 1 bug (fixed), coverage 85%
- Wrote docs/test-report.md
- sessions_send → main: Testing passed, report attached

### 17:38 [main] Phase 8 Final Audit Started
- todo #8 🔄
- sessions_spawn → auditor: Final audit

### 17:50 [auditor] Phase 8 Completed
- todo #8 ✅
- Deliverables: docs/checklist.md (16/18 passed, 2 NA)
- Deliverables: docs/audit-issues.md (AUDIT-001 Found → Fixed → Verified → Closed)
- Pre-audit baseline traceability: Fully covered
- Process compliance: coder-tester direct interaction trail complete ✅
- Final audit conclusion: Approved, cleared for delivery

### 17:52 [main] Phase 9 Merge + README
- todo #9 ✅
- git merge feature/user-auth → main
- README.md initial draft completed
- Delivered to user
```

---

## Audit Closed-Loop Record (Sub-pattern)

When an audit discovers issues, append a closed-loop record in journey.md:

```markdown
### HH:mm [auditor] Audit Issue Register AUDIT-001
- Severity: P0/P1/P2/P3 | Category: Completeness/Security/Testability/Constitution Consistency/Ambiguity
- Issue: [description] | Baseline Reference: [cite spec/constitution]

### HH:mm [coder] AUDIT-001 Fixed
- Root Cause: [analysis] | Solution: [description] | Affected Files: [list]

### HH:mm [tester] AUDIT-001 Verified
- Result: ✅/❌ | Regression Scope: [test cases] | Regression Result: ✅/⚠️

### HH:mm [auditor] AUDIT-001 Closed
- Final Adjudication: ✅ Approved / 🔁 Continue Fixing / 📝 Technical Debt | Reason: [rationale]
```

See issue management template `projects/ma/auditor/audit-report-template.md` for details.

---

## Test Bug Closed-Loop Record (Sub-pattern)

```markdown
### HH:mm [tester] Bug Register BUG-001 (Iteration Round N)
- Severity: P0/P1/P2/P3 | Module: [name]
- Symptom: [description] | Repro: [steps] | Expected vs Actual: [comparison]

### HH:mm [coder] BUG-001 Fixed
- Root Cause: [analysis] | Solution: [description] | commit: [hash]

### HH:mm [tester] BUG-001 Verified
- Original Case ✅/❌ | Regression: [N items] ✅/⚠️ | Conclusion: Close / Reopen
```

See defect template `projects/ma/tester/test-report-template.md` for details.

---

## Iteration Tracking Table

| Round | Triggered By | Fixer | Verifier | Result | todo.md | journey.md |
|------|------|--------|--------|------|---------|-----------|
| 1 | BUG-001 NPE | coder | tester | ✅ | Subtask appended | 17:20-17:30 |
| 2 | | | | | | |
| 3 | | | | | | |

> Iteration cap: 3 rounds. Main intervenes if exceeded.

---

## Main Decision Log Format

When main makes a decision in the following scenarios, a record must be appended to journey.md:

```markdown
### HH:mm [main] Decision [ID]
- Trigger Condition: [Audit failure / Iteration cap exceeded / Requirement change / Opinion divergence / Constitution conflict]
- Decision Content: [Accept / Fix / Technical debt / Change requirement / Amend constitution]
- Decision Rationale: [reasoning, cite spec/constitution clauses]
- Impact Scope: [affected phases / agents / files]
- Executor: [who executes this decision]
```

**Decision Trigger Conditions:**
1. Auditor final audit checklist not 100% passed
2. Coder-tester iteration exceeds 3 rounds
3. Auditor-tester opinion divergence
4. Requirement change involves constitution principles
5. Constitution conflicts with requirements
