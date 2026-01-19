# Logic Specification: backtest_app

## 1. Variable Dictionary & Traceability
| Variable | Scope | Source of Truth | Mutability | Related Req ID |
| :--- | :--- | :--- | :--- | :--- |
| `Profile` | Config | `configs/*` | Mutable | US-002 |
| `Tickers` | Runtime | Resolved profile | Mutable | US-001 |
| `MarketDataRequest` | Runtime | `shared_core.models.market_data` | Immutable | US-001, US-003 |
| `MarketFrequency` | Config | `shared_core.models.frequency` | Immutable | US-001 |
| `BacktestMetricsSettings` | Config | `shared_core.models.backtest` | Mutable | US-001 |
| `PortfolioValue` | Runtime | `vectorbt.Portfolio.value()` | Mutable | US-001 |
| `AggregatedValue` | Runtime | `_aggregate_series` | Mutable | US-001 |
| `EquityCurve` | Output | Report JSON | Immutable | US-001, US-007 |
| `MetricsStats` | Output | `vectorbt` stats | Immutable | US-001 |
| `MissingDataCount` | Output | Data QA | Immutable | US-008 |

## 2. Business Logic & Formulas

### 2.1 Aggregation (统一口径)
给定多资产组合价值矩阵 $V_{t,i}$ (时间 $t$，资产 $i$)：

- **Total** (默认):
  $$ V^{total}_t = \sum_i V_{t,i} $$
- **Mean**:
  $$ V^{mean}_t = \frac{1}{N}\sum_i V_{t,i} $$
- **Median**:
  $$ V^{median}_t = \text{median}_i(V_{t,i}) $$

聚合口径来自 `BacktestMetricsSettings.aggregation`，并用于：
- `metrics` 统计
- `equity_curve` 输出

### 2.2 年化常量
使用 `TRADING_DAYS_PER_YEAR = 252` (来自 `docs/system_maps/domain_logic.md`) 作为默认年化因子。

### 2.3 缺失数据计数

- **缺失行**: 当聚合前的 `PortfolioValue` 在某一时刻所有列均缺失或不可用时，判定为缺失行。
- **缺失数量**:
  $$ MissingDataCount = |\{t \mid \forall i, V_{t,i} \text{ is NaN}\}| $$

> 该计数应随报告输出，作为数据质量分析的一部分。

## 3. Event-Driven Lifecycle (How Variables Change)

- **`MarketDataRequest`**
  - `OnBacktestStart` -> 构建请求，完成频率归一化
- **`PortfolioValue`**
  - `OnDataLoaded` -> 生成回测组合价值曲线
- **`AggregatedValue`**
  - `OnMetricsCompute` -> 按聚合口径折叠为单列
- **`MetricsStats`**
  - `OnStatsCompute` -> 使用聚合后的单列计算指标
- **`EquityCurve`**
  - `OnReportRender` -> 使用聚合后的单列生成输出
- **`MissingDataCount`**
  - `OnReportRender` -> 统计缺失行数并写入报告

## 4. Truth Table (Decision Matrix)
| Situation | Input Value | Resulting Action |
| :--- | :--- | :--- |
| 多资产组合 | `PortfolioValue` 多列 | 先聚合成单列，再统计与输出 |
| 缺失值存在 | 部分列缺失 | 保留其余列，聚合时忽略 NaN |
| 全列缺失 | 全部为 NaN | 计入 `MissingDataCount`，该行不参与统计 |
| 频率为空 | `None` / 空字符串 | 抛出 `ValueError` (频率必须归一化) |

## 5. Edge Case Handling

- **No Tickers**: 直接报错 `Profile has no tickers`。
- **Missing OHLC Fields**: 抛出 `KeyError`。
- **Empty Combined Data**: 报错 `Combined price data is empty`。
- **Unsupported Frequency**: 报错 `Unsupported frequency`。

## 6. Domain Validation

- [PASS] 组合净值按资产价值求和符合行业常规组合回测定义。
- [PASS] 年化因子使用 252 交易日符合常见权益类资产标准。

## 7. Global Logic Updates

- [PROPOSAL] 在 `docs/system_maps/domain_logic.md` 中补充“缺失数据行计数与输出”作为数据质量规则。
- [NOTE] 未发现 `BIZ_RULES.md`，因此无全局规则冲突检查。
