# 安全审查报告 (Security Review Report)

## 执行摘要 (Executive Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 1 |

> **一句话结论**: simulator 的模拟日期范围超出 pandas 可表示上限，导致回测链路在默认缺失 CSV 时崩溃。

## 范围 (Scope)

### 威胁模型 / 测试范围

聚焦 `backtest_app/data_providers/adapters/simulator.py` 的模拟数据生成逻辑，验证缺失 CSV 时的 fallback 行为是否可用、错误提示是否准确，以及是否影响 backtest 主链路稳定性。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | N/A | 无 | N/A | N/A |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 本地模拟数据生成无网络依赖；无需超时。
- [ ] **重试机制 (Retry)**: 当前无重试；若模拟生成失败，直接报错。
- [ ] **降级策略 (Fallback)**: 缺失 CSV 时的 fallback 逻辑失败后无替代路径。

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | simulator 生成日期范围 `1993-01-01` 到 `2999-12-31` 超出 pandas datetime64[ns] 上限，触发 `OutOfBoundsDatetime`，导致回测在缺失 CSV 时崩溃。 | `backtest_app/data_providers/adapters/simulator.py:71` | 将模拟日期范围裁剪到 pandas 可表示上限（如 `2262-04-11`），或按请求区间生成（仅生成需要的日期）。 |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 错误提示显示 “Missing CSV” 但实际根因是模拟生成溢出，易误导排查。 | `backtest_app/data_providers/adapters/simulator.py:41` | 在捕获异常时区分 OutOfBounds 等错误，提示“模拟生成失败”并附原异常。 |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? 回测执行在 simulator fallback 时崩溃。
2. Why? `pd.date_range` 生成日期超出可表示范围。
3. Why? 模拟数据被要求生成到 2999 年。
4. Why? 未考虑 pandas datetime64[ns] 的上限约束。
5. Why? 缺乏针对 fallback 逻辑的边界测试。
-> **Root Cause**: 模拟日期范围未做上限裁剪，超出 pandas 可表示范围。

## 复现步骤 (Reproduction Steps)

```bash
python run.py --mode backtest --profile profile_A
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
from __future__ import annotations

from datetime import date
from pathlib import Path

from backtest_app.data_providers.adapters.simulator import SimulatorProvider
from shared_core.schemas.market_data import MarketDataRequest


def test_simulator_fallback_respects_pandas_bounds(tmp_path: Path) -> None:
    provider = SimulatorProvider(csv_dir=tmp_path, date_column="date", price_column="close")
    request = MarketDataRequest(tickers=["QQQ"], start=date(2020, 1, 1), end=date(2020, 1, 2))

    data = provider.fetch(request)

    frame = data["QQQ"]
    assert frame.index.min().date() == date(2020, 1, 1)
    assert frame.index.max().date() == date(2020, 1, 2)
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# start/end 超出 pandas datetime64[ns] 上限
# request.start > request.end
# date_column 缺失/格式异常
```

### 安全测试 (Security Tests)

```python
# 当前无网络/SQL/XSS 接口；若新增外部 provider，补充超时/异常处理测试
```

## 建议优先级 (Recommended Priority)

1. 限制/裁剪 simulator 生成日期范围到 pandas 支持范围（M1）。
2. 改善 fallback 失败时的错误提示（L1）。
