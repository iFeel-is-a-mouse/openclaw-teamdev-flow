# tester/test-report-template.md — Test Template Suite

> Tester uses this template for defect management and test reporting. Copy the corresponding sections to `docs/bugs.md` and `docs/test-report.md` when using.

---

# Part 1: Defect Report & Verification Closure

> Copy to `docs/bugs.md` and register defects one by one during testing.

## Test Session Info

| Field | Value |
|-------|-------|
| Project Name | `<ProjectName>` |
| Test Branch | `feature/xxx` |
| Test Date | `YYYY-MM-DD` |
| Tester | tester |

## Defect Summary

| ID | Severity | Symptom Summary | Root Cause | Fixed By | Verification Result | Iteration Round |
|----|----------|-----------------|------------|----------|---------------------|-----------------|
| BUG-001 | | | | | ⬜ | 1 |

## Defect Details

### BUG-001

**Severity:** P0 / P1 / P2 / P3 | **Module:** `<ModuleName>` | **Case:** `TC-XXX`

**Symptom:** [Description] | **Expected:** [What should happen] | **Actual:** [What actually happened]

**Reproduction Steps:** 1. [Step 1] 2. [Step 2] 3. [Step 3]

**Fix (coder fills in):** Root Cause: [Analysis] | Approach: [Description] | Files: [List] | Commit: [hash]

**Verification (tester fills in):** Original Case ✅/❌ | Regression [N items] ✅/⚠️ | Conclusion: Close / Reopen

## Severity Levels

| P0 Blocker | P1 Critical | P2 Normal | P3 Suggestion |
|------------|-------------|-----------|---------------|

## Iteration Rules

- Interact directly with coder, capped at 3 rounds; update todo.md/journey.md each round
- Notify main at the first bug and upon final pass
- If cap exceeded, main intervenes to adjudicate

---

# Part 2: Test Report

> Copy to `docs/test-report.md` and fill in after testing is complete.

## Test Summary

| Metric | Value |
|--------|-------|
| Total Testable Items / Black-box Cases / White-box Cases | `<N> / <N> / <N>` |
| Passed / Failed / Skipped | `<N> / <N> / <N>` |
| Pass Rate | `<N>%` |

## Defect Statistics

| Severity | Count | Fixed | Unfixed |
|----------|-------|-------|---------|
| P0 | | | |
| P1 | | | |
| P2 | | | |
| P3 | | | |

## Testable Items Completion

| # | Testable Item | Method | Result |
|---|---------------|--------|--------|
| 1 | | Black-box / White-box | ✅/❌ |

## Environment

| OS | Language/Runtime | Test Tool | Commit |
|----|------------------|-----------|--------|
| | | | |

## Conclusion

- [ ] All P0/P1 defects closed
- [ ] Regression passed, no new defects introduced
- [ ] 100% testable items coverage
- [ ] Ready for auditor final review
