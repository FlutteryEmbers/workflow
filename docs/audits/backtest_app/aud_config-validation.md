# 安全审查报告 (Security Review Report)

## 执行摘要 (Executive Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 1 |

> **一句话结论**: 存在阻断默认 CLI 运行的配置校验错误，建议先修复再推广使用。

## 范围 (Scope)

### 威胁模型 / 测试范围

本次审查聚焦于 `configs/config.yaml` 与 `backtest_app/app/settings/loader.py` 的配置契约一致性、CLI 回测路径的可用性、以及相关测试覆盖，目标是识别导致运行失败的输入校验问题与回归风险。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | N/A | 无 | N/A | N/A |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 当前仅本地 CSV 读取，无网络调用；若引入外部 provider，应定义超时。
- [ ] **重试机制 (Retry)**: 目前无重试逻辑；外部数据源接入时需补齐。
- [ ] **降级策略 (Fallback)**: 目前无降级；建议为外部 provider 设计 fallback 到本地缓存。

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | `AppConfig` 对 `strategies`/`optimizations` 的类型要求为 `dict[str, dict]`，但 `configs/config.yaml` 使用了字符串值（`active`/`engine`/`study_name`），导致默认 CLI 运行直接崩溃（Crash）。 | `backtest_app/app/settings/loader.py:27` `backtest_app/app/settings/loader.py:31` `configs/config.yaml:11` `configs/config.yaml:17` | 统一配置契约：放宽类型（如 `dict[str, Any]`）或定义专用模型（`StrategyConfig`/`OptimizationConfig`）并调整 `config.yaml` 结构；补充 schema 校验与错误提示。 |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 测试未覆盖 `configs/config.yaml` 的默认配置形态，导致配置契约漂移未被发现。 | `tests/test_backtest_app/test_settings.py:10` | 添加针对默认配置形态的加载测试，避免回归。 |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? CLI 启动时报 Pydantic 校验错误并退出。
2. Why? `AppConfig` 期望 `strategies`/`optimizations` 的值为字典，但配置为字符串。
3. Why? 配置设计使用 `active: buy_and_hold` 和 `engine: optuna` 作为标识符。
4. Why? 未定义统一的配置契约/模型来约束并解释这些标识符。
5. Why? 缺少对默认配置形态的测试覆盖与 CLI 冒烟测试。
-> **Root Cause**: 配置契约与 Pydantic 模型不一致，且缺乏覆盖默认配置的测试，导致运行期崩溃未被提前发现。

## 复现步骤 (Reproduction Steps)

```bash
python run.py --mode backtest --profile profile_A
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
from __future__ import annotations

from pathlib import Path

from backtest_app.app.settings import SettingsLoader


def test_settings_loader_accepts_default_shape(tmp_path: Path) -> None:
    config_path = tmp_path / "config.yaml"
    config_path.write_text(
        """
profiles:
  profile_A:
    tickers: [QQQ]

strategies:
  active: buy_and_hold
  buy_and_hold: {}

optimizations:
  engine: optuna
  study_name: demo
  params:
    lookback_days: [60]
""",
        encoding="utf-8",
    )

    SettingsLoader().load(str(config_path))
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 缺失 strategies.active 或 optimizations.params 应给出明确错误提示
```

### 安全测试 (Security Tests)

```python
# 当前无网络/SQL/XSS 接口；若引入外部 provider，需要新增超时/异常处理测试
```

## 建议优先级 (Recommended Priority)

1. 修复配置契约与 `AppConfig` 类型不一致问题（M1）。
2. 增补默认配置形态加载测试（L1）。
3. 外部 provider 接入前补齐超时/重试/降级策略。
