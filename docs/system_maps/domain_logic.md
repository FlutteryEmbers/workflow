# Domain Logic Reference (Business Glossary)

> **Status**: Reference Only
> **Role**: Knowledge Base for global business concepts and formulas.

## 1. Glossaries (术语表)

| Term | Definition | Source |
| :--- | :--- | :--- |
| **ROI** | Return on Investment. Typically annualized. | Global Reference |

## 2. Core Formulas (核心公式参考)

> These are standard formulas used in the system. Deviations should be documented.

### 2.1 General Calculation

TBD

### 2.2 Data Quality Metrics

**Missing Data Rows (缺失数据行数)**

对多资产时间序列矩阵 $X_{t,i}$：

$$ MissingDataCount = |\\{t \\mid \\forall i, X_{t,i} \\text{ is NaN}\\}| $$

用于回测输出中的数据质量分析，统计全列缺失的时间点数量。

## 3. Global Constants (全局常量)

- `DEFAULT_CURRENCY`: "USD"
- `TRADING_DAYS_PER_YEAR`: 252

## 4. Compliance Notes (合规备注)

- **Negative Values**: Usually raise `ValueError` unless specifically handled.
- **Rounding**: 4 decimal places recommended for storage.
