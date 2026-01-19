# 安全审查报告 (Security Review Report)

## 执行摘要 (Executive Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 1 |

> **一句话结论**: 警告不由配置模式变更引起，根因是 `portfolio.stats()` 在多列对象上执行，`aggregation` 未生效，导致默认均值聚合与 `start_value` 计算异常。

## 范围 (Scope)

### 威胁模型 / 测试范围

审计 `backtest_app/app/services/runner.py` 中指标统计流程，验证新增 `metrics.aggregation` 是否生效，以及多资产回测下 `portfolio.stats()` 的警告来源与影响。

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
| M1 | Tech | `Metric 'start_value' raised an exception` 表明 stats 在多列对象上计算失败，部分指标结果不可靠。 | `backtest_app/app/services/runner.py:235` | 在 stats 前显式聚合成单列组合净值，或显式选择列，确保指标可计算。 |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | `Object has multiple columns. Aggregating using mean` 表示 `aggregation` 配置未生效，vectorbt 回退到默认均值聚合。 | `backtest_app/app/services/runner.py:237` | 校验 vectorbt 对 `agg_func` 的支持，必要时改为手动聚合或使用支持的参数（如 `group_by`/`column`）。 |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? `start_value` 指标报异常。
2. Why? `portfolio.stats()` 在多列对象上执行。
3. Why? `aggregation` 配置未真正影响 stats 行为。
4. Why? `stats` 可能不支持 `agg_func` 或忽略该参数。
5. Why? 未在 stats 之前显式聚合/选择单列数据。
-> **Root Cause**: 统计口径未显式单列化，依赖 vectorbt 默认聚合路径，导致部分指标异常与警告。

## 复现步骤 (Reproduction Steps)

```bash
pytest tests/test_backtest_app/test_runner_backtest.py -q
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
# def test_metrics_stats_uses_explicit_aggregation(monkeypatch, tmp_path):
#     # 构造多标的回测
#     # 断言 stats 计算无 warning，且 start_value 可正常返回
#     assert payload["metrics"]["start_value"] is not None
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 测试用例 1: 多标的组合，aggregation=total
# 测试用例 2: 多标的组合，aggregation=mean
```

### 安全测试 (Security Tests)

```text
// 无明显安全攻击面（为统计口径问题）。
```

## 建议优先级 (Recommended Priority)

1. 在 stats 前显式聚合组合价值或选择单列，避免 `start_value` 异常。
2. 确认 vectorbt 支持的聚合参数，必要时调整参数或转为手动聚合。
