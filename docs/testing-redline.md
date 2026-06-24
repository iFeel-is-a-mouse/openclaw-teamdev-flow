# MA Team Testing Redline Rules

> Version: v1.0 | Effective Date: 2026-06-03
> Scope: All agents (main, coder, tester, auditor, publicist, reviewer)

---

## 1. pytest Timeout Redline (Hard)

**Any pytest test execution must not exceed 10 minutes total.**

### Execution Method
```bash
# Standard verification command (excluding known hang modules)
cd projects/pyOV
source .venv/bin/activate
python -m pytest tests/ \
  --ignore=tests/e2e \
  --ignore=tests/test_cardinality_graph.py \
  -q --timeout=600 2>&1 | tail -10
```

### Timeout Handling Process
1. Record start time when test launches
2. If not finished after 10 minutes, forcefully terminate the test process
   - Send SIGTERM first → wait 5 seconds
   - If not exited, send SIGKILL
3. Record the timed-out module, mark as **⚠️ Needs Manual Inspection**
4. Timeout does not block other modules from continuing execution

### Known Problem Modules
| Module | Issue | Handling |
|--------|-------|----------|
| `test_cardinality_graph.py` | Server fixture may hang | Skip or execute separately |
| `test_real_data.py` | Large dataset slow to load | Execute separately, excluded from total time limit |
| `tests/e2e/` | End-to-end tests | Separate CI execution; may skip during development |

---

## 2. Concurrent Test Restriction

**Simultaneous launch of multiple test processes is prohibited.**

- Only one pytest instance may run at a time
- Check before launching: `ps aux | grep pytest`
- If a test is already running, new tests must wait or cancel the existing test

---

## 3. Violation Handling

| Violation | Handling Measure |
|-----------|-----------------|
| Test timeout at 10 minutes | Terminate process, log, notify main |
| Concurrent launch of multiple tests | Cancel the later-launched test, notify relevant agent |
| `--timeout` parameter not used | Flagged during tester review, coder fixes |

---

## 4. Acceptance Criteria

**Development phase acceptance criteria: 765+ tests passed**

```bash
# Quick verification (excluding known hang modules)
python -m pytest tests/ \
  --ignore=tests/e2e \
  --ignore=tests/test_cardinality_graph.py \
  -q --timeout=600
```

---

## 5. Update Record

- **2026-06-24: v1.1** — Added LLM-driven coverage testing principles from real-world validation
  - Source: 45-round LLM-driven coverage testing project real-world validation
  - Countermeasures: coverage strategy codified, role boundary redlines, coverage-quality gap awareness
- **2026-06-03: v1.0** — Established redline rules
  - Trigger event: 20:22 coder sub-session deepseek failure + test process long-term occupation causing system freeze
  - Countermeasures: timeout control + concurrency restriction + problem module marking

---

## 6. LLM-Driven Coverage Testing Principles (Verified in Real-World Iteration)

> The following principles were verified through LLM-driven coverage testing (0% → 100% branch coverage, 45 rounds of iteration).

### Coverage Strategy

1. **Do not modify source code** — Use native coverage tools (Coverage.py / Istanbul / JaCoCo / gcov / go cover); do not build custom instrumentation. The "don't modify source" design choice of all mature tools is not a coincidence — it is the industry's repeatedly validated optimal path.
2. **Unconditional assertions** — LLM only needs to invoke functions to trigger branch execution; no need to guess return values. Coverage tools automatically track branch coverage. Invocation success = coverage achieved.
3. **Test accumulation** — Passing tests are permanently retained; failing tests are deleted. Clear coverage and rerun all each round; LLM only fills new gaps. Coverage naturally climbs, only rising, never falling.
4. **Prioritize pure functions** — Functions with clear input→output mappings are easiest to cover and yield the highest ROI. Functions that return file paths require the `open(path).read()` pattern.
5. **Complete condition context** — Give the LLM the entire `if/elif/else` block, not just a single line. LLMs need full context to generate correct parameters that trigger specific branches.

### Role Separation Redlines

- **Testing is the tester's responsibility, not the coder's.** Coder self-testing cannot replace tester independent verification. Key versions with coverage jumps exceeding 5% must trigger independent tester regression.
- **Auditor is responsible only for discovering issues and testability analysis; tester is responsible for writing tests to resolve them.** The testable items checklist identified in the audit report must be verified item by item by the tester.
- **Reviewer must independently participate in code review, not be merged with auditor.** Design review and code review are two independent lines of defense. The same person reviewing their own work from a different angle creates blind spots.

### Prohibited

- 🚫 **Substituting coverage numbers for test quality judgment.** 100% branch coverage ≠ effective testing. Tautological assertions (`assert x or not x`) and incorrectly guessed assertions can coexist with 100% coverage. After coverage targets are met, supplement with Mutation Testing to verify assertion quality.
- 🚫 **Testing without reading spec/design.** Without knowing the requirements, testing is random exploration, not systematic verification.
- 🚫 **Running full test suites without designing test cases.** Random exploration cannot guarantee coverage across all test dimensions, nor can it enable precise regression reproduction. Every critical version must produce structured test cases.
