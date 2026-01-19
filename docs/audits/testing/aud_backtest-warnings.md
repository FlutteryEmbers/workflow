# 安全审查报告 (Security Review Report)

## 执行摘要 (Executive Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 2 |

> **一句话结论**: 风险可控，但需消除频率缺失导致的指标偏差，并清理遗留警告。

## 范围 (Scope)

### 威胁模型 / 测试范围

针对测试输出中的告警进行审计，范围涵盖：
- `backtest_app/engines/optimizer/engine.py` 的 Pydantic v2 兼容性
- `backtest_app/engines/backtest/engine.py` 与 `backtest_app/app/services/runner.py` 的频率与指标输出
- `backtest_app/app/services/runner.py` 的多列序列聚合逻辑

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | Tech | N/A | N/A | N/A |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 本次范围内无外部网络调用（N/A）。
- [ ] **重试机制 (Retry)**: 本次范围内无外部网络调用（N/A）。
- [ ] **降级策略 (Fallback)**: 本次范围内无外部网络调用（N/A）。

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | 如果 `BacktestConfig.frequency` 未设置，vectorbt 统计会发出 “requires frequency” 警告并导致指标偏差。当前仅由调用方传入频率，缺少引擎层强制校验。 | `backtest_app/engines/backtest/engine.py:38` | 在引擎层强制频率必填（或提供默认），并为未设置频率抛出明确错误。 |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 若仍有旧代码使用 `result.dict()`，Pydantic v2 会产生弃用警告，后续升级可能破坏。 | `backtest_app/engines/optimizer/engine.py:48` | 统一使用 `model_dump()` 并在相关测试中覆盖。 |
| L2 | Tech | 多列序列的聚合逻辑可能掩盖真实资产维度（之前警告提示 “Aggregating using mean”）。当前用求和可能仍需业务确认。 | `backtest_app/app/services/runner.py:175` | 明确期望的权益曲线维度（总资产 vs 单资产），避免隐式聚合。 |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? 向量化回测统计指标缺少频率。
2. Why? 引擎未要求 `BacktestConfig.frequency` 必填。
3. Why? 频率责任只在调用方，未在引擎层兜底。
4. Why? 频率规范与校验未被强制为运行时约束。
5. Why? 先前实现以最小闭环优先，忽略了指标完整性校验。
-> **Root Cause**: 引擎层缺少频率必填约束，导致统计指标缺乏基础时间尺度。

## 复现步骤 (Reproduction Steps)

```python
from backtest_app.engines.backtest import BacktestConfig, BacktestEngine
import pandas as pd

price = pd.DataFrame({"AAA": [100, 101]}, index=pd.date_range("2020-01-01", periods=2))
engine = BacktestEngine()
# frequency=None 将触发 vectorbt 频率警告
engine.run(BacktestConfig(price=price, entries=price>0, exits=price<0, frequency=None))
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
import pytest
import pandas as pd
from backtest_app.engines.backtest import BacktestConfig, BacktestEngine


def test_backtest_requires_frequency():
    price = pd.DataFrame({"AAA": [100, 101]}, index=pd.date_range("2020-01-01", periods=2))
    with pytest.raises(ValueError):
        BacktestEngine().run(BacktestConfig(price=price, entries=price>0, exits=price<0, frequency=None))
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 频率别名归一化
from shared_core.models.market_data import MarketDataRequest
assert MarketDataRequest(tickers=["AAA"], frequency="D").frequency.value == "1d"
```

### 安全测试 (Security Tests)

```python
# N/A - 本次范围不涉及外部输入或安全边界
```

## 建议优先级 (Recommended Priority)

1. 补充频率必填校验或默认值，消除 vectorbt 频率警告。
2. 清理旧代码中的 `result.dict()` 用法，避免 Pydantic v2 弃用问题。
3. 明确权益曲线聚合策略，避免多列隐式聚合偏差。
