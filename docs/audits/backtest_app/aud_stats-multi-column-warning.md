# 安全审查报告 (Security Review Report)

## 执行摘要 (Executive Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 0 |
| 🟡 低风险 | 1 |

> **一句话结论**: 警告源于 `portfolio.stats()` 对多列组合默认做均值聚合，不是由配置模式变更引起；属于统计输出含义不明确的低风险问题。

## 范围 (Scope)

### 威胁模型 / 测试范围

针对回测运行时 `portfolio.stats()` 的警告进行审计，检查是否由配置模式变更触发，以及多资产组合下指标统计的语义是否存在误导风险。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | Tech | 无 | - | - |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 不适用（无网络调用）。
- [ ] **重试机制 (Retry)**: 不适用（无网络调用）。
- [ ] **降级策略 (Fallback)**: 不适用（无网络调用）。

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | 无 | - | - |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | `portfolio.stats()` 在多资产组合下默认对列均值聚合并产生警告，指标语义可能不清晰。 | `backtest_app/app/services/runner.py:219` | 明确指定统计口径（如选择单列、或显式聚合方式），并在配置中允许选择。 |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? 出现 “Object has multiple columns. Aggregating using mean” 警告。
2. Why? `portfolio.stats()` 遇到多列数据时默认做均值聚合。
3. Why? 当前实现未显式指定统计口径或选择列。
4. Why? 报告逻辑假设组合统计即可，不区分资产维度。
5. Why? 缺少对多资产统计语义的明确业务约束。
-> **Root Cause**: 多资产组合统计口径未显式定义，导致 vectorbt 使用默认均值聚合并警告。

## 复现步骤 (Reproduction Steps)

```bash
pytest tests/test_backtest_app/test_runner_backtest.py -q
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
# def test_runner_stats_multi_column_explicit_aggregation(monkeypatch, tmp_path):
#     # 准备多标的回测
#     # 断言 stats 使用显式列或显式聚合方式，不再触发警告
#     assert metrics_payload["aggregation"] == "mean"
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 测试用例 1: 多标的回测，metrics 选择单标的列
# 测试用例 2: 多标的回测，metrics 显式指定聚合方式
```

### 安全测试 (Security Tests)

```text
// 无明显安全攻击面（为统计语义问题）。
```

## 建议优先级 (Recommended Priority)

1. 明确多资产统计的业务语义并在 stats 调用处显式设置。
2. 在配置中增加 metrics 聚合方式或选择列的参数（如需要）。
