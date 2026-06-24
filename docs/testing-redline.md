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

## 6. LLM 驱动覆盖测试原则（实战验证）

> 以下原则经 LLM 驱动覆盖率测试实战（0% → 100% 分支覆盖，45 轮迭代）验证。

### 覆盖策略

1. **不改源码** — 使用原生覆盖率工具（Coverage.py / Istanbul / JaCoCo / gcov / go cover），不自研插桩。所有成熟工具的"不改源码"设计选择不是巧合，是行业反复验证的最优路径。
2. **无条件断言** — LLM 只需调用函数触发分支执行，不猜测返回值。覆盖率工具自动统计分支覆盖情况。调用成功即有效。
3. **测试累积制** — 通过测试永久保留，失败测试删除。每轮清覆盖率重跑全部，LLM 只补新缺口。覆盖率自然爬升，只涨不跌。
4. **优先纯函数** — 输入→输出明确的纯函数最容易覆盖，投资回报率最高。返回文件路径的函数需配合 `open(path).read()` 模式。
5. **完整条件上下文** — 给 LLM 整个 `if/elif/else` 块，不只单行。LLM 需要完整上下文才能生成触发分支的正确调参。

### 分工红线

- **测试是 tester 职责，不是 coder 的。** Coder 自测不能替代 tester 独立验证。覆盖率跳升超过 5% 的关键版本必须触发 tester 独立回归。
- **Auditor 只负责发现问题和可测性分析，tester 负责写测试解决。** 审计报告中识别的可测试项清单必须由 tester 逐项核验。
- **Reviewer 必须独立参与代码审查，不与 auditor 合并。** 设计审查和代码审查是两道独立防线。同一人从不同角度审查自己的工作会形成盲区。

### 禁止

- 🚫 **用覆盖率数字代替测试质量判断。** 100% 分支覆盖 ≠ 测试有效。恒真断言（`assert x or not x`）和断言猜测错误可伴随 100% 覆盖率同时存在。覆盖率达标后需补充 Mutation Testing 验证断言质量。
- 🚫 **不读 spec/design 就测试。** 不知道需求是什么，测试就是随机探索而非系统性验证。
- 🚫 **只跑全量不设计用例。** 随机探索无法保证覆盖所有测试维度，也无法在回归时精确复现。每个关键版本必须产出结构化测试用例。
