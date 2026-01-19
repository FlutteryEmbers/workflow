# 安全审查报告 (Security Review Report)

## 范围 (Scope)

### 威胁模型 / 测试范围

审计目标：在测试目录已重命名后，`pytest` 仍然出现 `ModuleNotFoundError: backtest_app`。重点检查导入路径可见性与重命名残留的影响。

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
| M1 | Tech | `backtest_app` 仍不可导入，说明 pytest 运行时未包含 repo root 的可见性 | `tests/test_backtest_app/test_*.py` | 明确导入路径策略（推荐：测试入口注入 repo root 到 `sys.path` 或使用 `python -m pytest`） |
| M2 | Tech | Traceback 仍指向旧路径 `tests/backtest_app/*`，疑似存在重命名残留或旧路径仍被收集 | 输出日志路径 | 清理旧目录与缓存（检查 `tests/backtest_app` 与 `__pycache__` 是否仍在） |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 顶层 `__init__.py` 保留旧导出，可能在未来引发额外导入错误 | `__init__.py:1` | 迁移或清理旧导出（非当前阻塞项） |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? `pytest` 仍然无法导入 `backtest_app`。
2. Why? 测试执行时 `backtest_app` 未在 Python 模块搜索路径内。
3. Why? 测试入口没有显式加入 repo root；运行方式可能未保证 `.` 在 `sys.path`。
4. Why? 重构后移除 `pytest.ini`/`setup.py`，未补充新的导入策略。
5. Why? 测试基础设施尚未重新标准化。

-> **Root Cause**: 缺少明确的测试导入路径策略（repo root 未进入 `sys.path`）。

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
# 1) 在 repo 根目录执行 pytest
# 2) 在 tests/ 子目录执行 pytest
```

### 安全测试 (Security Tests)

```python
# N/A: 当前问题为导入路径可见性，不涉及安全攻击面
```

## 总结 (Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 2 |
| 🟡 低风险 | 1 |

## 建议优先级 (Recommended Priority)

1. 明确测试导入路径策略（确保 repo root 在 `sys.path`）
2. 清理旧目录与缓存，避免 pytest 收集旧路径
3. 清理根包旧导出
