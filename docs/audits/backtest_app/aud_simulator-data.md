# 安全审查报告 (Security Review Report)

## 执行摘要 (Executive Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 1 |

> **一句话结论**: 默认配置在缺少本地 CSV 数据时直接崩溃，属于可用性阻断问题，需补齐数据准备或更友好的提示。

## 范围 (Scope)

### 威胁模型 / 测试范围

以默认 `configs/config.yaml` + `simulator` provider 作为输入，验证 CLI 回测路径的数据依赖、错误处理与可用性，重点关注缺失 CSV 数据时的行为是否符合预期与可诊断性。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | N/A | 无 | N/A | N/A |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 本地 CSV 读取无超时需求；若接入外部 provider 必须补齐。
- [ ] **重试机制 (Retry)**: 本地文件读取无重试；外部数据源接入需明确重试策略。
- [ ] **降级策略 (Fallback)**: 当前无 fallback（例如缺失数据时提示下载或使用示例数据）。

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | 默认配置指向 `data/simulator`，但仓库中没有该目录/CSV，导致 CLI 回测在无提示指引的情况下崩溃（Crash），阻断新用户使用。 | `configs/config.yaml:22` `backtest_app/data_providers/adapters/simulator.py:31` | 提供示例数据或初始化脚本；或在 CLI/runner 中加入预检查，输出可操作的提示（包含期望路径）。 |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 错误提示仅包含 ticker，不包含 `csv_dir` 路径，使排查成本增加。 | `backtest_app/data_providers/adapters/simulator.py:34` | 报错信息包含完整路径（例如 `Missing CSV: data/simulator/QQQ.csv`）。 |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? 运行 backtest 时抛出 `FileNotFoundError`。
2. Why? `SimulatorProvider` 在读取 `data/simulator/{ticker}.csv` 时文件不存在。
3. Why? 默认配置依赖本地 CSV 数据，但仓库未附带示例数据。
4. Why? 缺少数据准备脚本/文档指引或运行前校验。
5. Why? 将数据准备视为外部前置条件，但未在 CLI/文档中显式提示。
-> **Root Cause**: 默认配置依赖的本地 CSV 数据未提供且缺少预检查与指引。

## 复现步骤 (Reproduction Steps)

```bash
python run.py --mode backtest --profile profile_A
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
from __future__ import annotations

from pathlib import Path

import pytest

from backtest_app.data_providers.adapters.simulator import SimulatorProvider
from shared_core.schemas.market_data import MarketDataRequest


def test_simulator_missing_csv_includes_path(tmp_path: Path) -> None:
    provider = SimulatorProvider(csv_dir=tmp_path, date_column="date", price_column="close")
    request = MarketDataRequest(tickers=["QQQ"], start=None, end=None)

    with pytest.raises(FileNotFoundError) as exc:
        provider.fetch(request)

    assert "QQQ.csv" in str(exc.value)
    assert str(tmp_path) in str(exc.value)
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 缺失 csv_dir 目录
# 缺失 date_column/price_column
# CSV 存在但为空或日期乱序
```

### 安全测试 (Security Tests)

```python
# 当前无网络/SQL/XSS 接口；若新增外部数据源，补充超时/异常处理测试
```

## 建议优先级 (Recommended Priority)

1. 在默认运行路径上提供数据准备指引或示例数据（修复 M1）。
2. 改善缺失数据时的错误提示（修复 L1）。
3. 外部 provider 引入前补齐 resilience 策略。
