# 差异分析报告 (Gap Analysis Report)

> **Source Requirements**: `{{link to req_*.md}}`
> **Target Scope**: `{{description of scope}}`

## 1. 现状与目标 (As-Is vs To-Be)

| 维度 | 现状 (As-Is) | 目标 (To-Be) | 备注 |
| :--- | :--- | :--- | :--- |
| **功能/业务** | {{描述现状}} | {{描述需求}} | ... |
| **架构/技术** | ... | ... | ... |
| **数据** | ... | ... | ... |

## 2. 模式与规范提取 (Pattern Extraction)

> **Context**: 为了保持代码一致性，请遵循以下已识别的模式。

- **Directory Structure**:

  ```text
  {{Extract existing directory pattern}}
  ```

- **Coding Style**:
  - **Error Handling**: {{e.g. use Exceptions}}
  - **Layering**: {{e.g. Service returns DTO}}
  - **Naming**: {{e.g. *_service.py}}

## 3. 差距清单 (The Gap List)

### 🔴 Missing (缺失)
>
> *完全不存在的功能或模块，需要从头构建。*

- [ ] **M1**: {{Description}}
- [ ] **M2**: {{Description}}

### 🟡 Conflict (冲突)
>
> *现有逻辑与新需求存在冲突，需要修改。*

- [ ] **C1**: 原有逻辑 A 与新需求 B 冲突。建议：{{建议修改方案}}。

### ⚪ Redundant (冗余)
>
> *因新需求而不再需要的旧逻辑，建议废弃。*

- [ ] **R1**: {{Description}}

## 4. 实施建议 (Recommendations for Blueprint)

> *给架构师的建议：如何填补上述 Gap？*

- **Refinement**: 建议修正需求条款 AC1，因为...
- **Strategy**: 建议采用由内而外的迁移策略...

## 5. 拟定行动计划 (Proposed Action Plan)

> *Break down into Feature Tasks to fill the gaps.*

- [ ] **Feature 1**: `feat_{xxx}` - 负责实现 M1 (Core Logic).
- [ ] **Feature 2**: `feat_{yyy}` - 负责处理 C1 (Conflict Resolution).
- [ ] **Refactor**: `ref_{zzz}` - 先行清理 R1 (Redundant Code).
