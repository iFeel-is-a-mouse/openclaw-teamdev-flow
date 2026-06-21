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

- **2026-06-03: v1.0** — Established redline rules
  - Trigger event: 20:22 coder sub-session deepseek failure + test process long-term occupation causing system freeze
  - Countermeasures: timeout control + concurrency restriction + problem module marking
