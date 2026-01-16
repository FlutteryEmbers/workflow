# 安全审查报告 (Security Review Report)

## 范围 (Scope)

### 威胁模型 / 测试范围

{{在此填写第 1 步中定义的威胁模型或测试范围}}

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | {{Tech/Biz}} | {{问题描述}} | `{{文件:行号}}` | {{修复建议}} |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 所有的网络调用是否都有显式超时？
- [ ] **重试机制 (Retry)**: 是否处理了瞬时故障 (Transient Failures)？
- [ ] **降级策略 (Fallback)**: 如果依赖挂了，系统是否会雪崩？

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | {{Tech/Biz}} | {{问题描述}} | `{{文件:行号}}` | {{修复建议}} |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | {{Tech/Biz}} | {{问题描述}} | `{{文件:行号}}` | {{修复建议}} |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? {{原因 1}}
2. Why? {{原因 2}}
3. ...
-> **Root Cause**: {{根本原因}}

## 复现步骤 (Reproduction Steps)

```bash
# 或者是 Python 脚本
curl -X POST /api/boom -d '...'
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
# def test_bug_reproduction():
#     setup()
#     trigger_bug()
#     assert expected_behavior
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```{{语言}}
// 测试用例 1: {{描述}}
{{测试代码}}
```

### 安全测试 (Security Tests)

```{{语言}}
// 测试用例 2: {{描述}}
{{测试代码}}
```

## 总结 (Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | {{数量}} |
| 🟠 中风险 | {{数量}} |
| 🟡 低风险 | {{数量}} |

## 建议优先级 (Recommended Priority)

1. {{最紧急的修复项}}
2. {{次要修复项}}
3. {{可延后的修复项}}
