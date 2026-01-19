# 安全审查报告 (Security Review Report)

## 范围 (Scope)

### 威胁模型 / 测试范围

本次审计针对 `test_run_backtest_writes_output` 失败：`vectorbt.Portfolio.from_signals` 报错 SizeType 不支持。目标是确认配置/调用是否与 vectorbt 预期一致，并评估是否需要修复。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | Tech | 未发现高风险问题 | - | - |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 不适用（本问题为参数错误）
- [ ] **重试机制 (Retry)**: 不适用
- [ ] **降级策略 (Fallback)**: 不适用

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | `size_type="targetpercent"` 不是 vectorbt 支持的 SizeType，导致回测失败 | `backtest_app/engines/backtest/engine.py` | 将 `size_type` 替换为 vectorbt 支持的 `percent`，或在 allocator 输出 `amount`/`value` 类型并匹配 SizeType |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | Pydantic v2 警告：`dict()` 已弃用 | `backtest_app/engines/optimizer/engine.py:48` | 替换为 `model_dump()`（不影响当前失败） |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? 回测时抛出 `ValueError` 指出 SizeType 不支持。
2. Why? `BacktestEngine` 使用了 `size_type="targetpercent"`。
3. Why? 该值来自自定义默认值，而非 vectorbt 允许枚举。
4. Why? 采用了其他框架的概念名称或未对齐 vectorbt 文档。
5. Why? MVP 阶段未覆盖 vectorbt 的 size_type 兼容性测试。

-> **Root Cause**: `size_type` 取值与 vectorbt 支持枚举不一致。

## 复现步骤 (Reproduction Steps)

```bash
pytest tests/test_backtest_app/test_runner_backtest.py -q
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
# def test_backtest_size_type_supported():
#     config = BacktestConfig(..., size_type="percent")
#     assert BacktestEngine().run(config)
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 使用 size_type=amount/value/percent 的组合，确保均可跑通
```

### 安全测试 (Security Tests)

```python
# N/A: 参数校验问题，不涉及安全攻击面
```

## 总结 (Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 1 |

## 建议优先级 (Recommended Priority)

1. 修正 `size_type` 为 vectorbt 支持枚举
2. 清理 Pydantic v2 弃用警告（可后置）
