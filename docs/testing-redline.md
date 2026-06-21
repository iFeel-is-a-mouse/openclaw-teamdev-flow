# MA 团队测试红线规则

> 版本: v1.0 | 生效日期: 2026-06-03
> 适用范围: 所有 agent (main, coder, tester, auditor, publicist, reviewer)

---

## 1. pytest 超时红线（硬性）

**任何 pytest 测试执行，总时长不得超过 10 分钟。**

### 执行方式
```bash
# 标准验证命令（排除已知 hang 模块）
cd projects/pyOV
source .venv/bin/activate
python -m pytest tests/ \
  --ignore=tests/e2e \
  --ignore=tests/test_cardinality_graph.py \
  -q --timeout=600 2>&1 | tail -10
```

### 超时处理流程
1. 测试启动时记录开始时间
2. 超过 10 分钟未结束时，强制终止测试进程
   - 先发 SIGTERM → 等待 5 秒
   - 未退出则发 SIGKILL
3. 记录超时模块，标记为 **⚠️ 需人工检查**
4. 超时不阻塞其他模块继续执行

### 已知问题模块
| 模块 | 问题 | 处理方式 |
|------|------|---------|
| `test_cardinality_graph.py` | 服务器 fixture 可能 hang | 跳过或单独执行 |
| `test_real_data.py` | 大数据集加载慢 | 单独执行，不计入总时限 |
| `tests/e2e/` | 端到端测试 | 单独 CI 执行，开发阶段可跳过 |

---

## 2. 并发测试限制

**禁止同时启动多个测试进程。**

- 一次只运行一个 pytest 实例
- 启动前检查：`ps aux | grep pytest`
- 已有测试在运行时，新测试必须等待或取消旧测试

---

## 3. 违规处理

| 违规行为 | 处理措施 |
|---------|---------|
| 测试超时 10 分钟 | 终止进程，记录日志，通知 main |
| 并发启动多个测试 | 取消后启动的测试，通知相关 agent |
| 未使用 `--timeout` 参数 | tester 审核时标记，coder 修复 |

---

## 4. 验收标准

**开发阶段验收标准：765+ tests passed**

```bash
# 快速验证（排除已知 hang 模块）
python -m pytest tests/ \
  --ignore=tests/e2e \
  --ignore=tests/test_cardinality_graph.py \
  -q --timeout=600
```

---

## 5. 更新记录

- **2026-06-03: v1.0** — 制定红线规则
  - 触发事件: 20:22 coder 子会话 deepseek 故障 + 测试进程长期占用导致系统卡死
  - 应对措施: 超时控制 + 并发限制 + 问题模块标记
