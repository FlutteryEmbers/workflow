# 差异分析报告 (Gap Analysis Report)

> **Source Requirements**: `docs/requirements/backtest/req_backtest_app.md`
> **Target Scope**: backtest_app 回测/优化/报告全流程（含 shared_core 复用与 data provider 切换）

## 1. 现状与目标 (As-Is vs To-Be)

| 维度 | 现状 (As-Is) | 目标 (To-Be) | 备注 |
| :--- | :--- | :--- | :--- |
| **功能/业务** | 组合级回测已打通（profiles/meta -> simulator CSV -> buy_and_hold -> vectorbt -> JSON 输出），仅支持单 profile 单次 backtest | 支持多 profile 批量对比、策略可选与仓位管理、参数优化、可视化与导出 | 现状集中在 `backtest_app/app/services/runner.py` 与 `backtest_app/engines/backtest/engine.py` |
| **架构/技术** | 分层结构已成型（app/engines/data_providers/reporter/shared_core），策略/指标/优化多为占位或未串联 | 可替换 provider/strategy/indicator/optimizer/report 形成闭环；CLI 多模式路由 | optimizer 已有引擎但未接入 runner（`backtest_app/engines/optimizer/engine.py`） |
| **配置/数据** | 单一 `configs/config.yaml`，profiles/meta 已实现；仅 1d 频率；simulator 仅本地 CSV/伪数据 | YAML 分层配置、更多频率支持、外部数据源可缓存为 CSV | `MarketDataRequest` 频率固定为 `1d` |
| **输出/可视化** | `outputs/` 仅写入 JSON；ReportBuilder 对 dict 序列化为字符串 | 支持收益/回撤可视化、结构化指标输出与 CSV/Excel 导出 | `backtest_app/reporter/builder.py` |

## 2. 模式与规范提取 (Pattern Extraction)

> **Context**: 为了保持代码一致性，请遵循以下已识别的模式。

- **Directory Structure**:

  ```text
  TradingAssistance/
  ├── backtest_app/
  │   ├── app/              # cli, settings, services
  │   ├── engines/          # backtest, optimizer
  │   ├── data_providers/   # adapters, registry
  │   ├── reporter/
  │   ├── integration/
  │   └── app_utils/
  ├── shared_core/          # strategies, inventory, schemas, core_utils
  ├── configs/
  ├── outputs/
  └── tests/
  ```

- **Coding Style**:
  - **Error Handling**: 依赖/参数校验前置，主要抛出 `ValueError`/`RuntimeError`。
  - **Layering**: `cli -> services -> engines/providers -> reporter`，配置使用 Pydantic 模型校验。
  - **Naming**: 模块 snake_case，类 CamelCase，配置键 snake_case。
  - **Utilities**: app 侧只通过 `backtest_app/app_utils` 访问 YAML。

## 3. 差距清单 (The Gap List)

### 🔴 Missing (缺失)
>
> *完全不存在的功能或模块，需要从头构建。*

- [ ] **M1**: 策略注册/选择与参数化配置（不局限于 buy_and_hold），含仓位管理策略接口。
- [ ] **M2**: 多 profile 批量回测 + 汇总对比结果输出（US-002）。
- [ ] **M3**: 优化流程端到端编排（读取 optimizations -> 回测评估 -> 对比汇总/落盘）。
- [ ] **M4**: 可视化输出（收益曲线/回撤曲线）与交互式切换（US-007）。
- [ ] **M5**: 外部数据 provider 适配层与 CSV 落盘/缓存闭环（US-003/US-008）。
- [ ] **M6**: shared_core 指标接口与指标集合实现（US-009/US-011）。
- [ ] **M7**: CSV/Excel 导出能力（US-012）。
- [ ] **M8**: 实盘检测/对账接口边界与差异报告（US-013）。
- [ ] **M9**: 多项目/多入口 CLI 路由（Scope: 支持多模块入口选择）。

### 🟡 Conflict (冲突)
>
> *现有逻辑与新需求存在冲突，需要修改。*

- [ ] **C1**: CLI 暴露 `--mode optimize/report`，但 `run()` 仅支持 backtest（接口与行为不一致）。
- [ ] **C2**: `ReportBuilder` 对 dict 序列化为字符串，导致指标/曲线无法结构化输出（与可视化/导出需求冲突）。
- [ ] **C3**: `MarketDataRequest.frequency` 固定 `1d` 且 simulator 仅支持 1d，无法满足“可选频率”。

### ⚪ Redundant (冗余)
>
> *因新需求而不再需要的旧逻辑，建议废弃。*

- [ ] **R1**: `--mode report` 目前仅为 CLI 占位，如短期不实现可移除以避免误导。

## 4. 实施建议 (Recommendations for Blueprint)

- **Refinement**: 明确 YAML 字段规范（profiles/meta/strategies/optimizations）与分层覆盖规则。
- **Strategy**: 先打通 `backtest -> optimize -> report` 最小闭环，再扩展多组合对比与多 provider。
- **Data**: 建议先落地外部数据抓取 + CSV 缓存 + provider 切换，再完善频率扩展。
- **Interfaces**: shared_core 内补齐 Strategy/Indicator/Inventory 契约，减少 app 层对第三方库的直接依赖。

## 5. 拟定行动计划 (Proposed Action Plan)

- [ ] **Feature 1**: `feat_strategy_registry` - 策略/仓位管理接口与注册选择（M1）。
- [ ] **Feature 2**: `feat_profile_compare` - 多 profile 回测对比与汇总输出（M2）。
- [ ] **Feature 3**: `feat_optimize_pipeline` - 优化流程编排与评估输出（M3）。
- [ ] **Feature 4**: `feat_report_visuals` - 可视化与结构化输出修复（M4/C2）。
- [ ] **Feature 5**: `feat_data_provider_ext` - 外部数据 provider + CSV 落盘缓存（M5/C3）。
- [ ] **Feature 6**: `feat_indicators_core` - shared_core 指标接口与实现（M6）。
- [ ] **Feature 7**: `feat_export_outputs` - CSV/Excel 导出能力（M7）。
- [ ] **Feature 8**: `feat_live_check_contract` - 实盘检测接口与差异报告定义（M8）。
- [ ] **Feature 9**: `feat_cli_multi_entry` - 多项目/多入口 CLI 选择（M9/C1）。
