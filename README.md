# AI 辅助开发工作流 (AI-Assisted Development Workflow)

> **Philosophy**: **Code is the Definition of Done (Code First)**.
> 文档是代码的快照与契约。我们通过一系列标准化的任务，确保文档与代码的**双向一致性**，并利用 Agent 实现模块的**自描述**与**自维护**。

## 🌐 上下文流转图 (Context Flow)

这是一个基于“领域驱动”和“代码为王”的闭环系统。

```mermaid
graph TD
    %% 阶段定义
    subgraph Discovery [🔍 探索与定义]
        Understand(task_understand<br/>理解现状) --> Requirements(task_requirements<br/>需求分析)
        Requirements --> Spike(task_spike<br/>技术探针)
    end

    subgraph Design [📐 设计与规划]
        Requirements -->|Approved| Understand_Gap(task_understand<br/>差异分析/Gap)
        Understand_Gap -->|Gap Report| Blueprint(task_blueprint<br/>架构设计)
        Blueprint --> Feature(task_feature<br/>实施总控)
        
        %% Feature 的多源输入
        Refactor(task_refactor<br/>重构提案) -->|Proposal| Feature
        Spike -->|Feasibility Report| Feature
        Audit(task_audit<br/>问题审查) -->|Refined Issue| Feature
    end
    
    subgraph Execution [🏗️ 实施与落地]
        Feature -->|Implementation Plan| Coding(task_coding<br/>代码实现)
        Coding -->|Auto-Log| ModuleDoc(MODULE.md<br/>模块自文档)
    end

    subgraph Governance [🛡️ 治理与维护]
        Coding -->|New Code| Maintain(task_maintain<br/>一致性守护)
        Maintain -->|Reconcile| ModuleDoc
        Maintain -->|Deprecate/Fix| Docs(docs/**)
        Maintain -->|Alert| Blueprint
        
        Maintain -->|Fix Proposals| Update(task_update<br/>智能修订)
        Audit -.->|Doc Fixes| Update
    end

    %% 跨阶段连接
    Understand --> Refactor
    Spike -.->|Arch Impact| Blueprint
```

## 📂 任务清单 (Task Catalog)

任务定义位于 `workflow_core/tasks/`。所有文档均采用 **Domain-Centric** Naming Law (`docs/{Features}/{Domain}/{Intent}.md`)。

### 核心生产流 (Production)

| 任务文件 | 角色 (Role) | 目标 (Goal) | 输出路径 |
| :--- | :--- | :--- | :--- |
| **[task_understand](workflow_core/tasks/task_understand.md)** | 探险家 | **[双模式]** 生成地图 (Map Mode) 或 差异分析 (Gap Mode)。 | `docs/system_maps/` or `blueprints/` |
| **[task_requirements](workflow_core/tasks/task_requirements.md)** | 分析师 | 产出结构化 PRD。 | `docs/requirements/{Domain}/` |
| **[task_spike](workflow_core/tasks/task_spike.md)** | 起草人 | 技术可行性验证 (Doc + Sandbox Code)。 | `docs/spikes/{Topic}/` |
| **[task_blueprint](workflow_core/tasks/task_blueprint.md)** | 架构师 | 基于 Gap Analysis 设计架构方案。 | `docs/blueprints/{Scope}/` |
| **[task_feature](workflow_core/tasks/task_feature.md)** | TDD 专家 | **[总控]** 实施计划 (含熔断检查与模式选择)。 | `docs/features/{Domain}/` |
| **[task_spec](workflow_core/tasks/task_spec.md)** | 精算师 | **[立法]** 业务逻辑去模糊化与公式定义。 | `docs/specs/{Domain}/`, `domain_logic.md` |
| **[task_coding](workflow_core/tasks/task_coding.md)** | 工程师 | **[执行]** 编码 + **自动维护 MODULE.md**。 | *src/{Module}/*, `MODULE.md` |

### 治理与维护流 (Governance)

| 任务文件 | 负责角色 | 描述 | 输出/行为 |
| :--- | :--- | :--- | :--- |
| **[task_refactor](workflow_core/tasks/task_refactor.md)** | 精修师 | 产出重构提案。 | `docs/refactors/{Target}/` |
| **[task_audit](workflow_core/tasks/task_audit.md)** | 找茬员 | 深度审计与 Arch Rule 检查。 | `docs/audits/{Focus}/` |
| **[task_update](workflow_core/tasks/task_update.md)** | 编辑 | **[智能]** 带影响分析与回滚机制的文档修订。 | *原位修改*, *Archive 备份* |
| **[task_maintain](workflow_core/tasks/task_maintain.md)** | 守护者 | **[校准]** Code-First 双向一致性修复与初始化。 | *Sync MODULE.md*, *Deprecate Docs* |

## � 指令详解 (Command Details)

### 1. `task_understand` (理解现状)

- **Role**: 探险家 (Explorer)
- **何时使用**: 接手新项目、需要全局视野、或准备开发前想评估现状。
- **核心能力**:
  - **Map Mode**: 扫描全项目，生成 ASCII 系统地图和高层摘要。
  - **Gap Mode**: (Input: Requirements) 分析现有代码与新需求的差距，输出 `gap_analysis.md`。

### 2. `task_requirements` (需求分析)

- **Role**: 分析师 (Analyst)
- **何时使用**: 只有一个模糊的想法，需要澄清为可执行的需求。
- **核心能力**: 追问澄清，输出包含 User Stories 和 Acceptance Criteria 的 `req_{intent}.md`。

### 3. `task_spike` (技术探针)

- **Role**: 起草人 (Drafter)
- **何时使用**: 技术方案不确定、需要验证性能、或对比第三方库。
- **核心能力**:
  - **Sandbox**: 在 `.sandbox/` 目录运行实验代码，隔离生产环境。
  - **Report**: 输出 `sp_{topic}.md`，回答“能不能做”并提供原型代码。

### 4. `task_blueprint` (架构设计)

- **Role**: 架构师 (Architect)
- **何时使用**: 需求已明确，Gap 已识别，需要规划技术实现路径。
- **核心能力**: 选型、分层设计、定义关键接口，输出 `bp_{scope}.md`。

### 5. `task_spec` (规格定义) -- *New!*

- **Role**: 精算师 (Actuary)
- **何时使用**: 需求模糊、公式不确定、或需要审计代码逻辑。
- **核心能力**:
  - **De-ambiguity**: 将自然语言转化为真值表和公式 `spec_{intent}.md`。
  - **Librarian**: 维护全局 `docs/system_maps/domain_logic.md`。

### 6. `task_feature` (实施规划)

- **Role**: TDD 专家 (TDD Pro)
- **何时使用**: 准备开始写代码，需要将蓝图拆解为原子化的实施步骤。
- **核心能力**:
  - **总控**: 执行意图分类和重构熔断检查。
  - **Porting**: 负责将 Spike 代码逻辑“移植”进生产设计。
  - **Plan**: 输出 `feat_{intent}.md`，详细到函数签名。

- **Plan**: 输出 `feat_{intent}.md`，详细到函数签名。

### 7. `task_coding` (代码实现)

- **Role**: 工程师 (Engineer)
- **何时使用**: 有了 Implementation Plan，开始从伪代码变真代码。
- **核心能力**:
  - **Execution**: 严格遵循 Plan 编码。
  - **Auto-Doc**: 自动创建/更新 `src/{module}/MODULE.md`。
  - **Modes**: 支持 Safety (默认), Pragmatic, Refactor 模式。

### 8. `task_refactor` (重构提案)

- **Role**: 精修师 (Refactorer)
- **何时使用**: 代码有坏味道、结构混乱、需治理技术债务。
- **核心能力**: 识别 Code Smells，提出不破环业务行为的重构方案 `ref_{target}.md`。

### 9. `task_audit` (问题审查)

- **Role**: 找茬员 (Validator)
- **何时使用**: 发现 Bug、进行安全扫描、或 Code Review。
- **核心能力**: 深度分析根因，检查 `ARCH_RULES` 合规性，输出 `aud_{focus}.md`。

### 10. `task_maintain` (一致性守护)

- **Role**: 守护者 (Human Interface Guard)
- **何时使用**: 文档与代码不一致、项目初始化、或定期巡检。
- **核心能力**:
  - **Reconcile**: 强制以 Code 为真理更新 Living Docs (`MODULE.md`, Maps)。
  - **Aggregation**: 自动聚合生成 `api_catalog.md`。
  - **Drift Report**: 输出架构偏离和 Schema 变更的体检报告。

### 11. `task_update` (智能修订)

- **Role**: 编辑 (Editor)
- **何时使用**: 需求变更导致需要更新旧文档。
- **核心能力**: 回滚备份，智能更新文档内容并评估影响。

## �🚀 关键机制 (Key Mechanisms)

### 1. 模块自文档化 (Module Self-Documentation)

- **MODULE.md**: 每个业务模块根目录下的“简历”。
- **Auto-Update**: `Coding Task` 结束时，Agent 会自动：
  - 更新 Public API 签名。
  - 追加 **Distributed Changelog**（标注跨模块的 Trigger 关系）。

### 2. 领域驱动命名 (Domain-Centric Naming)

文档不再是一大堆 `yyyy_mm_dd` 的文件，而是结构化的知识库：

- `docs/features/user/login.md` (User 域的登录功能)
- `docs/blueprints/backtest/engine-optimization.md` (Backtest 域的优化蓝图)
- **备份策略**: 每次覆盖前，旧文件自动移入 `archive/` 子目录。

### 3. 一致性校准 (Code-First Reconciliation)

- **Maintain Task** 拥有最高裁决权：**代码是唯一的真理来源**。
- **Jurisdiction**: 仅维护存续性文档 (Living Docs)。Feature/Blueprint 等事务性文档 (Transactional Docs) 视为历史档案，只读不改。

### 4. 最佳实践 (Best Practices)

- **Impact Check**: 修改文档超过 10% 或涉及架构时，强制暂停确认。
- **Ghost Check**: 修改后主动猎杀逻辑矛盾的残余文本。

### 5. 软版本控制 (Soft Versioning Policy)

- **Local Time Machine**: 所有文档生成任务在覆盖文件前，都会自动将旧版本移动到 `archive/` 目录。
- **Naming**: `{dirname}/archive/{filename}.bak_{timestamp}.md`。
- **Value**: 即便没有 Git Commit，Agent 的每一次尝试也都可追溯、可回滚。构建了文件系统的“撤销栈”。

### 6. 模式化执行 (Mode-Based Execution)

Coding Task 根据风险等级强制分为三种模式，通过 Prompt 约束 Agent 行为：

- **Safety Mode (默认)**: 严禁修改配置和接口，仅允许写业务逻辑。
- **Pragmatic Mode**: 允许受控的配置变更（需注释）。
- **Refactor Mode**: 仅允许代码清理和重构，严禁变更业务逻辑。

### 7. 逆向工程能力 (Reverse Engineering)

Maintain 和 Update 任务具备“阅读代码理解意图”的能力。

- 它们不依赖过时的文档，而是直接从 AST（抽象语法树）或源码结构反推当前的设计状态。
- 这是 **Code-First** 哲学的核心技术支撑。

### 8. 命名规范 (Naming Convention)

为了避免不同类型文档在 IDE 中同名混淆，所有生成的文档必须强制使用以下前缀：

- **PRD**: `req_{intent}.md` (e.g., `req_login.md`)
- **Blueprints**: `bp_{scope}.md` (e.g., `bp_auth.md`)
- **Features**: `feat_{intent}.md` (e.g., `feat_login.md`)
- **Refactors**: `ref_{target}.md` (e.g., `ref_order.md`)
- **Audits**: `aud_{focus}.md` (e.g., `aud_security.md`)
- **Specs**: `spec_{intent}.md` (e.g., `spec_margin.md`)
- **Spikes**: `sp_{topic}.md` (e.g., `sp_redis.md`)

> **注意**: 严禁生成不带前缀的裸文件名（如 `login.md`）。

### 9. Mermaid 语法红线 (Mermaid Syntax Guard)

为了防止渲染错误，编写 Mermaid 时必须遵守：

1. **强制引号**: 所有节点文本必须使用双引号包裹。
    - ❌ 错误: `A[User (Admin)]`
    - ✅ 正确: `A["User (Admin)"]`
2. **ID 规范**: 节点 ID 只能使用英文字母和下划线，严禁空格。
    - ❌ 错误: `User Login --> System`
    - ✅ 正确: `user_login["User Login"] --> System`

### 10. 智能工作流增强 (Workflow Enhancements)

- **Gap Analysis (差异分析)**:
  - 集成在 `task_understand` 中。
  - **Gap Mode**: 输入 Code + Requirements，输出差距报告 (`gap_analysis.md`)，作为 Blueprint 的输入。

- **Pattern Extraction (模式提取)**:
  - 在 `task_understand` 中执行。
  - 自动提取目录结构和隐含风格，确保后续设计的一致性。

- **Feature Pre-Flight (飞行前检查)**:
  - `task_feature` 在设计前强制执行：意图分类 (Intent)、重构熔断 (Circuit Breaker) 和模式选择 (Mode Selection)。
  - **Fail Fast**: 代码太烂或不适合新功能时，立即熔断并建议重构。

- **Sandbox Experiments (沙盒实验)**:
  - `task_spike` 专属能力。
  - 在 `.sandbox/` 目录下运行真实代码验证假设，拒绝纸上谈兵。
  - **隔离协议**: 严禁 Spike 任务修改 `src/`。

## 🎬 场景指南 (Scenario Guide)

不知道该用哪个 Task？即使有说明书也容易迷路？请参考以下常见剧本：

### 🎭 剧本 A: "我有一个新点子" (New Feature)

1. **执行 `task_requirements`**: 输入你的想法，产出 `docs/requirements/user/signup.md`。
2. **执行 `task_understand`**: (Gap Mode) 输入 PRD，分析现状与目标的差距，产出 `docs/blueprints/user/gap_analysis_signup.md`。
3. **执行 `task_blueprint`**: 输入 Gap 报告，设计填补差距的架构方案，产出 `docs/blueprints/user/auth-system.md`。
4. **执行 `task_feature`**: 输入架构设计的一块（如 API），产出 `docs/features/user/auth-api.md`。
   - *(Optional)* 如果技术不确定，先执行 `task_spike` 在 `.sandbox/` 里跑通原型。
5. **执行 `task_coding`**: 输入 Feature 文档，让 AI 写代码并自动维护文档。
6. **执行 `task_understand`**: (Map Mode) 最后更新 `system_maps` 以反映新领土。

### 🎭 剧本 B: "这代码跑不通/有Bug" (Bug Fix)

1. **执行 `task_audit`**: 输入错误日志，分析根因，产出 `docs/audits/maintenance/crash-report.md`。
2. **执行 `task_feature`**: (若是复杂逻辑错误) 基于审计报告制定修复方案。
3. **执行 `task_coding`**: (Logic Mode) 修复代码。
4. **执行 `task_maintain`**: 确保修复没有破坏其他契约。

### 🎭 剧本 C: "这代码写的太烂了" (Refactoring)

1. **执行 `task_understand`**: 生成当前模块的地图。
2. **执行 `task_refactor`**: 提出重构提案 `docs/refactors/order/legacy-cleanup.md`。
3. **执行 `task_feature`**: 将重构提案转化为实施步骤。
4. **执行 `task_coding`**: (Refactor Mode) 安全重构。

### 🎭 剧本 D: "刚接手一个旧项目" (Project Onboarding)

1. **执行 `task_maintain`**: (使用 Bootstrap 指令) "请初始化所有文档"，生成 `MODULE.md`。
2. **执行 `task_understand`**: "生成全局系统地图"。
3. **执行 `task_audit`**: "扫描现有架构风险"。

### 🎭 剧本 E: "外来代码集成" (Code Integration)

面对外部代码（开源仓库、遗留系统、同事发的 Zip 包）需要整合进 `src` 时：

1. **Ingest (隔离吞吐)**: 将代码放入 `.sandbox/incoming/{name}/`，严禁直接复制进 `src/`。
2. **Understand (建立认知)**: 执行 `task_understand`，分析沙盒中代码的业务逻辑和算法，忽略其不兼容的框架细节。
3. **Porting (移植重写)**: 执行 `task_feature` + `task_coding`。
    - **Prompt**: "参考 `.sandbox/...` 的逻辑，提取核心算法，用符合 `ARCH_RULES` 的新架构在 `src/` 中重写。"
    - **原则**: **逻辑提取与注入**，而不是文件合并。

## 📁 目录结构 (Structure)

```text
.workflow/
├── ARCH_RULES.md           # [Tech Law] 架构技术约束与红线
├── README.md               # 本文件
├── workflow_core/          # 核心定义
│   ├── roles/              # 角色 (Persona) 定义
│   ├── tasks/              # 任务 (Prompt) 定义
│   └── templates/          # 输出模版 (Templates)
└── docs/                   # [自动管理] 领域文档库
    ├── audits/
    ├── blueprints/
    ├── features/
    ├── refactors/
    ├── requirements/
    ├── specs/              # [New] 逻辑规格说明书
    ├── spikes/
    └── system_maps/
.sandbox/               # [NEW] 技术探针的实验靶场 (只进不出)
src/
└── {module}/
    ├── MODULE.md           # [自动维护] 模块状态与变更日志
    └── ...
```
