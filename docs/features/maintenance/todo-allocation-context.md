# 实现计划 (Implementation Plan)

## 概述 (Summary)

> **目标**: 将 PositionAllocator 升级为基于 AllocationContext 的接口，支持信号、价格、组合状态与约束驱动的仓位分配。
> **范围**:
>
> - [x] 核心: 引入 AllocationContext/PositionPlan 并替换 allocator 接口
> - [x] 边界: 等权 allocator 与回测链路适配新接口
> - [ ] 排除: 高级约束求解与策略优化（后续迭代）

## 需求 (Requirements)

### 核心接口定义 (Public Interface Design)

- **Class/Module**: `shared_core/inventory`
- **Method Signature**:

  ```python
  class AllocationContext(BaseModel):
      signals: Any
      prices: pd.DataFrame
      portfolio_state: Any
      constraints: dict[str, Any]

  class PositionAllocator(ABC):
      def allocate(self, context: AllocationContext) -> PositionPlan:
          """Allocate target positions."""
  ```

- **Reason**: 提升扩展性与跨服务复用能力，避免 allocator 仅依赖 price 的局限。

### 配置与环境 (Configuration & Environment)

- [ ] **Config File**: 无
- [ ] **Env Vars**: 无
- [ ] **CLI Args**: 无

### 数据变更 (Data Schema Changes)

- **JSON/Pydantic**:

  ```python
  class AllocationContext(BaseModel):
      signals: Any
      prices: pd.DataFrame
      portfolio_state: Any
      constraints: dict[str, Any]
  ```

### 依赖影响 (Dependency Impact)

- 无新增依赖（复用 pandas / pydantic）。

### 验收标准 (Acceptance Criteria)

- [ ] AC1: PositionAllocator 改为接收 AllocationContext。
- [ ] AC2: AllocationContext 至少包含 signals/prices/portfolio_state/constraints。
- [ ] AC3: 等权分配与回测链路可在新接口下运行。
- [ ] AC4: 文档与接口一致，旧接口标记为 deprecated。

### 备选方案 (Alternatives)

- **方案 A (Minimalist Strategy)**: 保持 `allocate(prices)`，通过闭包或全局状态注入 signals/约束。
  - [ ] ❌ 驳回 (理由: 接口隐式、难测试、难复用)
- **方案 B (Robust Strategy)**: 新增 AllocationContext 并替换 PositionAllocator 接口。
  - [ ] ✅ 采纳 (理由: 接口清晰，可扩展)

## 约束与复用检查 (Constraints & Reuse)

- [ ] **配置检查**: 否
- [ ] **接口检查**: 是（PositionAllocator 签名变更）
- [ ] **复用分析**:
  - 需实现功能: context schema
  - 现有候选: `shared_core/schemas`
  - 决策: 新增 `AllocationContext`

## 影响分析 (Impact Analysis)

### 受影响范围 (Scope)

- **模块**: `shared_core/inventory`, `backtest_app/app/services/runner.py`
- **API**: PositionAllocator 公共接口变更
- **数据**: AllocationContext 作为新输入结构

### 风险 (Risks)

- 现有 allocator 与回测链路需要同步调整。
- 旧文档与实现不一致的技术债需被标注。

## 逻辑变更 (Logic Changes)

### 流程/状态对比 (Flow/State)

```mermaid
flowchart LR
    Old[Allocator prices] --> Output[weights]
```

```mermaid
flowchart LR
    New[Allocator context] --> Output[PositionPlan]
```

## 详细变更计划 (Detailed Changes)

### 1. 新增/修改文件: `shared_core/inventory/context.py`

- **变更类型**: [新增]
- **变更描述**:
  - 定义 AllocationContext / PositionPlan。

### 2. 新增/修改文件: `shared_core/inventory/base.py`

- **变更类型**: [修改]
- **变更描述**:
  - PositionAllocator 接口切换为 context。

### 3. 新增/修改文件: `shared_core/inventory/equal_weight.py`

- **变更类型**: [修改]
- **变更描述**:
  - 使用 context.prices 输出等权权重。

### 4. 新增/修改文件: `backtest_app/app/services/runner.py`

- **变更类型**: [修改]
- **变更描述**:
  - 构造 AllocationContext 并传入 allocator。

## 实施步骤 (Execution Steps)

1. [ ] 定义 AllocationContext/PositionPlan。
2. [ ] 更新 PositionAllocator 接口与实现。
3. [ ] 更新 runner 组装 context。
4. [ ] 补充/更新测试。

## 验证计划 (Verification Plan)

- **自动化测试**: allocator + runner 兼容性测试。
- **手动验证**: CLI 回测路径不回归。
