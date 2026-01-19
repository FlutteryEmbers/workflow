# 实现计划 (Implementation Plan)

## 验收标准列表 (Acceptance Criteria List)

- [ ] AC1: 当 provider=simulator 且缺失 CSV 时，CLI 输出包含完整路径的可操作错误提示。
- [ ] AC2: 仅在 simulator 路径执行预检查，不影响其他 provider 的真实数据加载流程。
- [ ] AC3: 测试覆盖 simulator 缺失 CSV 的错误信息（包含目录 + ticker）。

## 概述 (Summary)

> **目标**: 为 simulator 数据源增加前置校验与更明确的错误提示，避免缺失 CSV 时的“无指引崩溃”。
> **范围**:
>
> - [x] 核心: simulator 缺失数据时的诊断与提示
> - [x] 边界: 仅影响 simulator provider，不触碰真实数据 provider
> - [ ] 排除: 不引入新数据源/下载逻辑
>
> **建议执行模式**: Safety
> **任务类型**: Value Delivery (Type A)

## 需求 (Requirements)

### 核心接口定义 (Public Interface Design)

- **Class/Module**: `backtest_app/data_providers/adapters/simulator.py`
- **Method Signature**:

  ```python
  class SimulatorProvider(MarketDataProvider):
      def fetch(self, request: MarketDataRequest) -> dict[str, "pd.DataFrame"]:
          ...
  ```

- **Reason**: 不修改公共接口，仅在内部增强错误提示。

### 配置与环境 (Configuration & Environment)

- [ ] **Config File**: 无
- [ ] **Env Vars**: 无
- [ ] **CLI Args**: 无

### 数据变更 (Data Schema Changes)

- 无

### 依赖影响 (Dependency Impact)

- 无新增依赖。

### 验收标准 (Acceptance Criteria)

- [ ] AC1: 当缺失 CSV 时，错误包含 `csv_dir` + `ticker.csv` 完整路径。
- [ ] AC2: 仅 simulator 生效；其他 provider 不受影响。
- [ ] AC3: 新测试覆盖缺失 CSV 的错误信息。

### 备选方案 (Alternatives)

- **方案 A (Minimalist Strategy)**: 仅增强 `FileNotFoundError` 的提示信息（包含完整路径）。
  - [ ] ✅ 采纳 (理由: 最小改动，不影响其他 provider)
- **方案 B (Robust Strategy)**: 在 runner 中加入 provider=simulator 的预检查，提示数据准备路径；若 provider != simulator，跳过检查。
  - [ ] ✅ 可选 (理由: 可提前给出更明确指引，但需额外逻辑)
- **方案 C**: 自动生成示例 CSV（硬编码样本数据）。
  - [ ] ❌ 驳回 (理由: 可能污染真实数据流程或引入误用风险)

## 约束与复用检查 (Constraints & Reuse)

- [ ] **配置检查**: 否
- [ ] **接口检查**: 否（不修改 public API）
- [ ] **复用分析**:
  - 需实现功能: 错误提示增强 / simulator 专用提示
  - 现有候选: `SimulatorProvider.fetch`
  - 决策: 复用并增强

## 影响分析 (Impact Analysis)

### 受影响范围 (Scope)

- **模块**: `backtest_app/data_providers/adapters/simulator.py`, `backtest_app/app/services/runner.py` (若选方案 B), `tests/test_backtest_app/test_simulator_provider.py`
- **API**: 无 Breaking Change
- **数据**: 无

### 风险 (Risks)

- 过度提示可能误导用户以为有自动下载功能（需明确“请自行准备 CSV”）。

## 逻辑变更 (Logic Changes)

### 流程/状态对比 (Flow/State)

```mermaid
flowchart LR
    old_cli["CLI run"] --> old_fetch["Simulator fetch"]
    old_fetch --> old_fail["FileNotFoundError (ticker only)"]
```

```mermaid
flowchart LR
    new_cli["CLI run"] --> new_fetch["Simulator fetch"]
    new_fetch --> new_fail["FileNotFoundError (full path + hint)"]
```

## 详细变更计划 (Detailed Changes)

### 1. 新增/修改文件: `backtest_app/data_providers/adapters/simulator.py`

- **变更类型**: [修改]
- **变更描述**:
  - 当 CSV 不存在时，抛出包含完整路径的错误信息：
    - `Missing CSV: {csv_dir}/{ticker}.csv (simulator provider)`
  - 保持现有逻辑不变，不影响其他 provider。

### 2. 新增/修改文件: `backtest_app/app/services/runner.py` (可选，仅方案 B)

- **变更类型**: [修改]
- **变更描述**:
  - 在 provider == "simulator" 时，若 `csv_dir` 目录不存在，提前抛出带指引的 ValueError。
  - 对非 simulator provider 不做检查。

### 3. 新增/修改文件: `tests/test_backtest_app/test_simulator_provider.py`

- **变更类型**: [修改]
- **变更描述**:
  - 增加断言：错误信息包含 `csv_dir` 与 `ticker.csv`。

## 实施步骤 (Execution Steps)

1. [ ] 修改 `backtest_app/data_providers/adapters/simulator.py`，完善缺失 CSV 的错误提示。
2. [ ] （可选）在 `backtest_app/app/services/runner.py` 加入 simulator 专用目录存在性检查。
3. [ ] 更新 `tests/test_backtest_app/test_simulator_provider.py`，验证错误提示。
4. [ ] 运行测试 `pytest -q tests/test_backtest_app/test_simulator_provider.py`。

## 验证计划 (Verification Plan)

- **自动化测试**: 新增/更新 simulator 缺失 CSV 的错误提示测试。
- **手动验证**: `python run.py --mode backtest --profile profile_A` 在缺失 CSV 时提示路径与数据准备指引。
