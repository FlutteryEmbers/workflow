# 安全审查报告 (Security Review Report)

## 执行摘要 (Executive Summary)

| 风险等级 | 数量 |
| :--- | :--- |
| 🔴 高风险 | 0 |
| 🟠 中风险 | 1 |
| 🟡 低风险 | 0 |

> **一句话结论**: 失败并非由配置模式变更直接导致，而是报告序列化逻辑未处理 `dict`，导致输出结构被扁平化为字符串。

## 范围 (Scope)

### 威胁模型 / 测试范围

本次审计聚焦于回测输出报告的序列化与配置读取链路，验证新增 `metrics` 配置能否正确写入输出 JSON。重点排查：配置结构变化是否导致字段丢失，以及报告生成逻辑是否破坏字段结构。

## 发现的问题 (Findings)

### 🔴 高风险 (Critical)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| C1 | Tech | 无 | - | - |

### 🛡️ 稳健性缺失 (Resilience Gaps)

> **针对外部依赖的检查结果**:

- [ ] **超时控制 (Timeout)**: 不适用（无网络调用）。
- [ ] **重试机制 (Retry)**: 不适用（无网络调用）。
- [ ] **降级策略 (Fallback)**: 不适用（无网络调用）。

### 🟠 中风险 (Medium)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| M1 | Tech | `ReportBuilder._serialize` 未处理 `dict`，导致报告内容被序列化为 `{"value": "{...}"}` 字符串，`metrics_config` 等字段丢失，触发 KeyError。 | `backtest_app/reporter/builder.py:19` | 增加对 `dict` 的显式序列化路径，确保输出为结构化 JSON。 |

### 🟡 低风险 (Low)

| ID | 类型 | 问题描述 | 位置 | 建议修复 |
| :--- | :--- | :--- | :--- | :--- |
| L1 | Tech | 无 | - | - |

## 根因分析 (Root Cause Analysis)

> **针对 Top 1 严重问题执行 5 Whys 分析**:

1. Why? 输出 JSON 中缺少 `metrics_config`。
2. Why? `ReportBuilder._serialize` 将整个 report dict 转成了字符串。
3. Why? `_serialize` 只处理 `dict()`/`to_dict()` 方法，不识别原生 `dict`。
4. Why? 设计假设 report 会是 Pydantic 或对象类型，而不是原生 dict。
5. Why? 缺少对报告输出契约的单元测试覆盖。
-> **Root Cause**: 报告序列化逻辑遗漏 `dict` 分支，导致输出结构性丢失。

## 复现步骤 (Reproduction Steps)

```bash
pytest tests/test_backtest_app/test_runner_backtest.py -q
```

## 建议的回归测试 (Suggested Regression Test)

> **Draft Code**: 此代码应在当前版本中**失败**，在修复后**通过**。请在 Feature Task 中优先落地此测试。

```python
# def test_report_serializes_dict_metrics_config(tmp_path):
#     payload = {"metrics_config": {"frequency": "1d", "annualization_factor": 252}}
#     out_path = ReportBuilder().render(payload, name="test")
#     data = json.loads(out_path.read_text(encoding="utf-8"))
#     assert data["metrics_config"]["frequency"] == "1d"
```

## 攻击性测试用例 (Attack Test Cases)

### 边缘情况测试 (Edge Cases)

```python
# 测试用例 1: report 为 dict 且嵌套结构
# payload = {"metrics": {"a": 1}, "list": [1, 2]}
# assert json.loads(ReportBuilder().render(payload).read_text()) == payload
```

### 安全测试 (Security Tests)

```text
// 无明显安全攻击面（本次问题为序列化逻辑缺陷）。
```

## 建议优先级 (Recommended Priority)

1. 修复 `ReportBuilder._serialize` 对 dict 的序列化逻辑。
2. 添加回归测试覆盖报告输出契约。
