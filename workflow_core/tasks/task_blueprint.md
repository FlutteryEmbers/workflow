# 蓝图任务 (Blueprint Task)

## 上下文注入 (Context Injection)

角色定义: {{CONTENT: /workflow_core/roles/architect.md}}
输出模版: {{CONTENT: /workflow_core/templates/blueprint.md}}

## 指令 (Instructions)

你现在将扮演 **架构师 (The Architect)** 的角色。

### 🛡️ Mermaid 语法红线 (Mermaid Syntax Guard)

为了防止渲染错误，编写 Mermaid 时必须遵守：

1. **强制引号**: 所有节点文本必须使用双引号包裹。
    * ❌ 错误: `A[User (Admin)]`
    * ✅ 正确: `A["User (Admin)"]`
2. **ID 规范**: 节点 ID 只能使用英文字母和下划线，严禁空格。
    * ❌ 错误: `User Login --> System`
    * ✅ 正确: `user_login["User Login"] --> System`
3. **避免 HTML**: 尽量不要在 Label 中使用 HTML 标签（如 `<br>`），除非必须。

### 第 1 步：理解需求 (Understand Requirements)

分析提供的 input (Requirements 或 Gap Analysis)，提取关键约束和目标。
**输出一段简短的"设计目标摘要 (Design Objective)"。**

### 第 2 步：技术选型 (Technology Selection)

为项目选择合适的技术栈：

* **语言/运行时**: 选择理由（如有必要，列出备选方案）
* **框架/库**:选择理由（如有必要，列出备选方案）
* **数据库/存储**: 选择理由（如有必要，列出备选方案）
* **其他工具**:选择理由

**关键：** 对于具有重大影响的决策（如数据库选型），请提供 2-3 个可行选项及其利弊分析，并给出你的推荐。

### 第 3 步：架构设计 (Architecture Design)

1. **目录结构**: 定义项目文件夹布局
2. **模块划分**: 划分核心模块及其职责
3. **依赖关系**: 描述模块间的依赖方向
4. **设计模式**: 显式声明核心组件将采用的设计模式（如单例、工厂、策略、观察者等），并解释原因。切勿为了用模式而用模式。
5. **核心抽象 (Core Abstractions)**: 定义系统的扩展点（如 `BaseHandler`, `IAdapter`）。列出基类名及其必须履行的职责（关键方法名），但不涉及具体签名。
6. **运维与观测 (Ops Strategy)**: 明确配置分层 (Conf/Env)、机密管理及日志策略 (Stdout/File, JSON/Text)。这决定了应用的可运维性。
7. **测试策略 (Testing Strategy)**:
    * 针对系统性质定义 Mock 级别。
    * **Level 1 (Light)**: CRUD/内部逻辑，优先集成测试，轻 Mock。
    * **Level 2 (Simulation)**: LLM/外部支付/硬件，必须建立独立的高保真 Simulator 模块。

### 第 4 步：输出蓝图 (Output Blueprint)

使用 `输出模版` 生成最终的技术蓝图文档。

* **重要：** 严禁在蓝图中包含具体的代码行（伪代码除外）。所有的实施细节必须留给 Feature Task。
* **重要：** 必须将宏大的蓝图拆解为若干个原子化的 Feature Task。

* **重要：** 每个技术决策都要附带简短的理由。

### 输出路径与命名 (Output Config)

**Base Resolution**:

1. Locate the **Workflow Root** directory.
   * It MUST contain `workflow_core/` (folder) AND `ARCH_RULES.md` (file).
2. All output paths below are relative to this `{WorkflowRoot}`.

**Target Path**:
`{WorkflowRoot}/docs/blueprints/{scope}/{filename}`

Naming format: `bp_{intent}.md` (example: `bp_system-arch.md`, `bp_trading-core.md`)
> **Constraint**: `{scope}` should describe the architecture level or module, e.g., `global`, `payment`.
>
> **Backup Policy (归档策略)**:
> 若目标文件已存在，严禁直接覆盖！
>
> 1. 读取旧内容。
> 2. 将旧文件重命名为 `archive/{filename}.bak_{yyMMdd_HHmm}.md`。
> 3. 写入新文件到原路径。

## 用户输入 (User Input)

{{需求文档或功能描述}}
