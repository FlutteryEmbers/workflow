# 安全审查报告 (Security Review Report)

## 范围 (Scope)

### 威胁模型 / 测试范围

审计目标：`test_run_backtest_writes_output` 失败于 `_series_to_records`，错误为无法将 pandas Series 转成 float。重点检查 equity curve 的形态与序列化逻辑是否匹配。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | Tech | 未发现高风险问题 | - | - |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 不适用
- [ ] **重试机制 (Retry)**: 不适用
- [ ] **降级策略 (Fallback)**: 不适用

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | `portfolio.value()` 返回多列 DataFrame/Series，`_series_to_records` 直接 `float(value)` 导致 TypeError | `backtest_app/app/services/runner.py:118` | 在序列化前统一聚合（例如 `portfolio.value().mean(axis=1)`）或选择单一列 |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 多个 stats 指标提示 `frequency` 未设置，仅为警告 | `backtest_app/app/services/runner.py` / vectorbt stats | 为 vectorbt 设置频率或在报告中标注警告 |
| L2 | Tech | Pydantic v2 `dict()` 弃用警告 | `backtest_app/engines/optimizer/engine.py:48` | 替换为 `model_dump()`（非当前失败） |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? `_series_to_records` 报错无法转 float。
2. Why? 传入的 value 是 pandas Series 而不是标量。
3. Why? `portfolio.value()` 在多标的时返回多列数据结构。
4. Why? 序列化逻辑假设单列/单标的。
5. Why? MVP 回测输出未定义多标的聚合规则。

-> **Root Cause**: equity curve 序列化未处理多标的输出结构。

## 复现步骤 (Reproduction Steps)

```bash
pytest tests/test_backtest_app/test_runner_backtest.py -q
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
# def test_equity_curve_serialization_handles_multi_columns():
#     series = portfolio.value()
#     records = _series_to_records(series)
#     assert records
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 多 ticker 下 equity curve 仍可序列化
```

### 安全测试 (Security Tests)

```python
# N/A: 数据序列化问题，不涉及安全攻击面
```

## 总结 (Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 2 |

## 建议优先级 (Recommended Priority)

1. 统一多标的 equity curve 的聚合或选择规则
2. 视需要设置 stats 频率与清理弃用警告
