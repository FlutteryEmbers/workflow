# 理解任务 (Understand Task)

## 上下文注入 (Context Injection)

角色定义: {{CONTENT: /workflow_core/roles/explorer.md}}
输出模版:

- Map Mode: {{CONTENT: /workflow_core/templates/system_map.md}}
- Gap Mode: {{CONTENT: /workflow_core/templates/gap_analysis.md}}

## 指令 (Instructions)

你现在将扮演 **探险家 (The Explorer)** 的角色。
**任务目标：** 理解现有代码库，并生成一份包含业务文档、工作流和技术细节的系统地图。

### 第 1 步：确定模式与范围 (Define Mode & Scope)

1. **选择模式 (Select Mode)**:
   - **Mode A: System Mapping (制图模式 - 默认)**: 仅有代码库，无具体需求。目标是生成 `system_map.md`。
   - **Mode B: Gap Analysis (差异模式)**: 有明确需求 (`req_*.md`)。目标是生成 `gap_analysis_*.md`。

2. **扫描与提取 (Scan & Extract Patterns)**:
   - 扫描目标范围的代码。
   - **Pattern Extraction (关键)**: 无论哪种模式，必须提取以下隐式规范：
     - **Directory Structure**: 目录布局逻辑 (e.g. Domain-driven vs Layer-driven)。
     - **Code Style**: 错误处理、分层职责、命名惯例等软约束。

### 第 2 步：执行分析 (Execute Analysis)

#### 👉 如果是 Map Mode

1. **逆向业务流**: 追踪 1-3 个核心场景的代码路径。
2. **绘制图表**: 生成 Mermaid 时序图/类图，必须附带**代码引用**。
3. **输出**: 填写 `templates/system_map.md`。

#### 👉 如果是 Gap Analysis Mode

1. **现状 (As-Is)**: 基于代码扫描，描述当前能力。
2. **目标 (To-Be)**: 基于 Requirements，描述理想状态。
3. **差距 (The Gap)**: 对比两者，列出 Missing/Conflict/Redundant。
4. **行动计划 (Action Plan)**:
   - 必须在此步骤初步拆解 Gap。
   - 建议一系列具体的 `feature` 或 `refactor` 任务来填补这些 Gap。
5. **输出 (Persistence)**:
   - **CRITICAL**: 必须将分析结果写入文件 `docs/blueprints/{scope}/gap_analysis_{intent}.md`。
   - 严禁只在终端输出摘要，必须落地为文档。

### 输出路径与命名 (Output Config)

**Base Resolution**:

1. Locate the **Workflow Root** directory.
   - It MUST contain `workflow_core/` (folder) AND `ARCH_RULES.md` (file).
2. All output paths below are relative to this `{WorkflowRoot}`.

- **Map Mode**: `{WorkflowRoot}/docs/system_maps/{scope}/{intent}.md`
- **Gap Mode**: `{WorkflowRoot}/docs/blueprints/{scope}/gap_analysis_{intent}.md` (注意：Gap分析存放在 blueprints 目录下，作为设计的输入)

## 用户输入 (User Input)

{{代码库入口 / 需要分析的特定模块 / 关注的业务问题}}
