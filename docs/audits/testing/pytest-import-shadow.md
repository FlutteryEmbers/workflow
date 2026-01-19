# 安全审查报告 (Security Review Report)

## 范围 (Scope)

### 威胁模型 / 测试范围

审计目标：`pytest` 收集阶段的 `ModuleNotFoundError` 仍未解决，重点检查是否存在包名冲突或导入路径遮蔽（shadowing），并确认修复策略。

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
| M1 | Tech | 旧 `tests/backtest_app` 作为包存在（有 `__init__.py`），与应用包 `backtest_app/` 同名冲突导致导入被 shadow | `tests/backtest_app/__init__.py`（旧路径） | 重命名为 `tests/test_backtest_app/`（当前方案）或移除测试目录的 `__init__.py` |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 顶层 `__init__.py` 仍保留旧模块导出，若被导入可能触发 ImportError | `__init__.py:1` | 迁移或清理旧导出（非当前阻塞项） |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? `pytest` 仍无法导入 `backtest_app.app` / `backtest_app.engines`。
2. Why? Python 先解析到 `tests/backtest_app` 包，而非应用目录 `backtest_app/`。
3. Why? `tests/backtest_app/__init__.py` 使测试目录成为与应用同名的包。
4. Why? pytest 将 `tests/` 放入 `sys.path`，导致包名冲突优先匹配。
5. Why? 重构后未统一测试目录命名规范，出现与应用包同名的目录。

-> **Root Cause**: `tests/backtest_app` 包名与应用包 `backtest_app` 同名，导致导入被 shadow（通过重命名为 `tests/test_backtest_app` 规避）。  

## 复现步骤 (Reproduction Steps)

```bash
pytest -q
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
# def test_import_backtest_app_modules():
#     import backtest_app.app
#     import backtest_app.engines
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 将 tests/backtest_app 重命名为 tests/test_backtest_app 后重复运行 pytest
```

### 安全测试 (Security Tests)

```python
# N/A: 当前问题为模块导入路径与包名冲突，不涉及安全攻击面
```

## 总结 (Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 1 |

## 建议优先级 (Recommended Priority)

1. 解除测试包名冲突（将 `tests/backtest_app` 重命名为 `tests/test_backtest_app` 或删除测试目录的 `__init__.py`）
2. 清理根包旧导出，避免未来隐性错误
