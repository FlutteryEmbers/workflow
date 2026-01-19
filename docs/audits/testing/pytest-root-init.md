# 安全审查报告 (Security Review Report)

## 范围 (Scope)

### 威胁模型 / 测试范围

本次审计针对 `pytest` 收集阶段新增的错误：`__init__.py` 在仓库根目录被当作测试模块导入，触发已移除模块（`data_sources` 等）导入失败。目标是定位触发路径、判断是否为预期行为，并给出修复建议。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | Tech | 未发现高风险问题 | - | - |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 不适用（本问题为导入失败）
- [ ] **重试机制 (Retry)**: 不适用
- [ ] **降级策略 (Fallback)**: 不适用

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | `pytest` 收集阶段会导入仓库根目录 `__init__.py`，其中仍引用已删除模块 `data_sources`，导致收集失败 | `__init__.py:22` | 移除旧导出/旧模块引用，或让 pytest 忽略根目录 `__init__.py` |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 根目录 `__init__.py` 仍保留旧模块文档注释与导出，可能误导后续使用者 | `__init__.py` | 清理或迁移至历史分支说明 |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? `pytest` 在收集阶段报错 `ModuleNotFoundError: data_sources`。
2. Why? 导入了仓库根目录 `__init__.py`，里面执行了旧的 `from data_sources import ...`。
3. Why? `pytest` 会导入可收集的包文件作为测试模块。
4. Why? 破坏式重构后删除了旧模块，但未同步清理 `__init__.py`。
5. Why? 根包仍保留旧时代的“聚合导出”设计。

-> **Root Cause**: 根目录 `__init__.py` 仍引用已删除模块，在 pytest 收集阶段被导入导致失败。

## 复现步骤 (Reproduction Steps)

```bash
pytest -q
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
# def test_root_package_importable():
#     import TradingAssistance  # 不应触发已删除模块的导入错误
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 运行 pytest 时包含 repo root 作为收集路径
```

### 安全测试 (Security Tests)

```python
# N/A: 本问题为导入路径与包残留引用，不涉及安全攻击面
```

## 总结 (Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 1 |

## 建议优先级 (Recommended Priority)

1. 清理根目录 `__init__.py` 中已删除模块的导出引用
2. 视需要配置 pytest 忽略根包文件（如通过 `collect_ignore`）
