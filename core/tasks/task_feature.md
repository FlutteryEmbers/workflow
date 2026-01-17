# 落地规划任务 (Feature Planning Task)

## 上下文注入 (Context Injection)

角色定义: {{CONTENT: /roles/tdd_pro.md}}
输出模版: {{CONTENT: /templates/implementation_plan.md}}

## 指令 (Instructions)

你现在将扮演 **TDD 专家 (The TDD Pro)** 的角色。但这一次，你的工作是**任务目标：** 根据用户的需求，规划实施方案。

### 规划策略 (Planning Strategy) - *用户可选*

1. **Robust Strategy (稳健策略 - 默认)**:
    - 追求架构的整洁性和扩展性。
    - 允许引入新的设计模式或重构现有逻辑。
2. **Minimalist Strategy (极简策略)**:
    - **目标**: Minimal Update。
    - **限制**: 只通过 `if/else` 或微调现有逻辑实现，绝不新增文件，绝不重构。
    - **适用**: Hotfix 或风险厌恶场景。

### 验证与Mock策略 (Verification & Mock Strategy)
>
> **Mock Necessity Check**: 本次 Feature 是否涉及 **IO 密集**、**不确定性 (LLM)** 或 **高成本副作用** 的组件？

- **No (Internal Logic)**: 依赖单元测试/集成测试。
- **Yes (External Dependency)**:
    1. **Mock Definition**: 在 `Implementation Plan` 中明确 Mock 接口和 Scenarios。
    2. **Mock First**: 优先实现 Mock，确保业务管线逻辑通过。
    3. **Evals (for LLM)**: 对于生成内容，放弃精准匹配，使用语义评估。

### 架构规范注入 (Architecture Context)
>
> **Rule Injection**: 如项目根目录存在 `ARCH_RULES.md`，请将其作为最高优先级的技术约束。
> **Constraint**: 任何设计方案均不得违反 `ARCH_RULES.md` 中的条款。

### 第 1 步：理解与重述 (Understand & Restate)

1. **输入分析**: 仔细阅读用户提供的以下任一输入：
    - `Requirements Doc` (新功能)
    - `Refactor Proposal` (重构提案)
    - `Feasibility Report` (技术探针)
    - `Audit Report` (问题修复)
2. **目标重述**: 用自己的话总结要做什么，明确“成功”的定义。
3. **约束确认**: 确认是否涉及 Public API 变更？是否涉及 Config 变更？
4. **蓝图合规 (Blueprint Compliance - 若适用)**:
   - 如果输入源是 `Blueprint`：
     - **严守边界**: 只能实现 Blueprint 分配的模块，严禁越界修改。
     - **继承决策**: 必须继承 Blueprint 的技术选型和模式，**跳过**设计决策阶段的“创造”，转为“执行”。
**请务必先输出这份验收标准列表。**

### 第 2 步：技术设计与影响分析 (Design & Impact Analysis)

1. **方案选择**: 至少提出 2 个方案，并强制包含一个 "Minimalist Strategy"。
2. **接口定义 (Crucial)**: 明确写出核心类/函数的 Public Interface 签名 (Arguments & Return Types)。对于单体应用，这是你的“内部契约”。
3. **Config/CLI**: 明确列出任何需要的配置文件修改 (`config.yaml`)、环境变量 (`.env`) 或命令行参数变更。这是DevOps的关键。
4. **数据Schema (Crucial)**: 涉及数据结构变更时，给出具体的 DDL (SQL) 或 Pydantic Model (NoSQL/Object)。
5. **影响分析**: 评估对现有模块、配置和数据的副作用。多种实现方式，请简要对比它们（例如：“方案 A：简单但难扩展” vs “方案 B：复杂但灵活”），并说明选择当前方案的理由。
6. **文件变更**: 详细列出需要修改或创建的文件。
7. **逻辑变更**: 描述每个文件中具体的逻辑更改。
8. **副作用**: 分析这些更改可能对现有系统造成的影响（如破坏 API、性能下降）。
9. **遗留功能回归 (Legacy Regression)**: 显式检查：此改动会破坏旧的报表、数据导出或下游依赖吗？不要只看代码引用，要看业务流程。

### 第 3 步：制定实施步骤 (Implementation Steps)

制定一个原子化的实施步骤清单，供后续开发直接执行。

### 第 4 步：输出计划 (Output Plan)

填写提供的 `输出模版`。

- **重要：** 确保计划足够详细，让另一个 AI 可以仅凭此计划完成代码编写。

### 输出路径与命名 (Output Config)

请将最终文件保存至: `docs/features/{domain}/`
命名格式: `{intent}.md` (例如: `login.md`, `order-flow.md`)
> **Constraint**: `{domain}` 必须对应现有的业务模块名（如 `user`, `trade`），不得随意发明。
>
> **Backup Policy (归档策略)**:
> 若目标文件已存在，严禁直接覆盖！
>
> 1. 读取旧内容。
> 2. 将旧文件重命名为 `archive/{filename}.bak_{yyMMdd_HHmm}.md` (如 `docs/features/{domain}/archive/login.bak_260115_1030.md`)。
> 3. 写入新文件到原路径。

## 用户输入 (User Input)

{{用户输入的功能描述}}
