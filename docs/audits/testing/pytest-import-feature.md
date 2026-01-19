# 安全审查报告 (Security Review Report)

## 范围 (Scope)

### 威胁模型 / 测试范围

审计范围：`pytest` 收集阶段的 `ModuleNotFoundError`，关注 `backtest_app.*` 模块不可导入的问题。目标是定位包可见性、导入路径与工程结构的根因，并判断是否必须立即修复。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | Tech | 未发现高风险问题 | - | - |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 不适用（本问题为本地导入失败）
- [ ] **重试机制 (Retry)**: 不适用
- [ ] **降级策略 (Fallback)**: 不适用

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | `pytest` 收集阶段无法导入 `backtest_app.*`，导致全部测试中断 | `tests/test_backtest_app/test_optimizer_outputs.py:7`, `tests/test_backtest_app/test_runner_backtest.py:7`, `tests/test_backtest_app/test_settings.py:7`, `tests/test_backtest_app/test_simulator_provider.py:6` | 明确包可见性：增加 `backtest_app/__init__.py` 或在测试入口显式加入 repo root 到 `PYTHONPATH`（二选一） |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 顶层 `__init__.py` 仍保留旧模块导出，若被导入将触发新的 ImportError | `__init__.py:1` | 清理旧导出或移至历史兼容分支（非当前阻塞项） |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? `pytest` 导入 `backtest_app.*` 失败。
2. Why? 运行测试时 `backtest_app` 没有被识别为可导入包。
3. Why? `backtest_app/` 缺少顶层 `__init__.py`，且测试环境未显式加入 repo root 到 `PYTHONPATH`。
4. Why? 破坏式重构后移除旧测试入口（如 `pytest.ini`/`setup.py`）未补上新的导入策略。
5. Why? 当前阶段优先搭骨架，测试基础设施尚未完成。

-> **Root Cause**: 缺少明确的“包可见性策略”（包入口/安装或测试入口路径注入）。

## 复现步骤 (Reproduction Steps)

```bash
pytest -q
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
# def test_import_backtest_app_modules():
#     import backtest_app.app
#     import backtest_app.engines.optimizer
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 直接在 repo 根目录与 tests/ 子目录下分别运行 pytest
# 确认导入路径一致性
```

### 安全测试 (Security Tests)

```python
# N/A: 当前问题为模块导入路径与包声明，不涉及安全攻击面
```

## 总结 (Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 1 |

## 建议优先级 (Recommended Priority)

1. 解决 `backtest_app` 包可见性（添加包入口或测试入口路径注入）
2. 清理根包旧导出（避免未来隐性导入错误）
