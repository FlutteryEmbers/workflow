# 安全审查报告 (Security Review Report)

## 范围 (Scope)

### 威胁模型 / 测试范围

本次审计聚焦于 `pytest` 收集阶段的导入失败（`ModuleNotFoundError: backtest_app.*`），验证原因是否来自包结构、`PYTHONPATH` 或测试入口配置，并评估是否需要在当前“骨架阶段”立即修复。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | Tech | 未发现高风险问题 | - | - |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 不适用（当前问题为本地导入失败）
- [ ] **重试机制 (Retry)**: 不适用
- [ ] **降级策略 (Fallback)**: 不适用

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | `pytest` 收集阶段无法导入 `backtest_app` 子模块，导致测试全部中断 | `tests/test_backtest_app/test_optimizer_outputs.py:7`, `tests/test_backtest_app/test_settings.py:7` | 明确项目作为包的可导入路径（例如：确保 repo root 在 `PYTHONPATH` 或提供测试入口/安装方式） |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 顶层 `__init__.py` 仍引用已删除模块（`data_sources`, `technical_analysis`, `utils`），后续若导入根包会触发新错误 | `__init__.py:1` | 移除旧导出或将其移至历史兼容分支（在骨架阶段可延后） |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? `pytest` 导入 `backtest_app.*` 失败。
2. Why? 执行测试时 `backtest_app` 未被识别为可导入模块。
3. Why? 测试运行环境未显式将项目根目录加入 `sys.path`，且项目未被安装为包。
4. Why? 破坏式重构后移除了 `setup.py/pytest.ini` 等传统入口，未补充新的测试入口说明。
5. Why? 当前处于“骨架阶段”，测试基础设施尚未完善。

-> **Root Cause**: 测试环境缺乏明确的包导入路径/安装策略，导致模块在 pytest 收集阶段不可见。

## 复现步骤 (Reproduction Steps)

```bash
pytest -q
```

> 备注：本地审计环境未提供 `python` 可执行文件，无法直接复现；以上复现步骤来自用户报告。

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
# 在 tests/ 目录下直接运行 pytest，验证是否仍能正确导入 backtest_app
```

### 安全测试 (Security Tests)

```python
# N/A: 当前问题为导入路径与测试执行配置，不涉及安全攻击面
```

## 总结 (Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 1 |

## 建议优先级 (Recommended Priority)

1. 解决 `pytest` 导入路径与包可见性问题（避免测试全部中断）
2. 清理根包 `__init__.py` 中的旧导出，防止后续隐性错误
