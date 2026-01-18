# 探针调研任务 (Spike Research Task)

## 上下文注入 (Context Injection)

角色定义: {{CONTENT: /workflow_core/roles/drafter.md}}
输出模版: {{CONTENT: /workflow_core/templates/spike_log.md}}

## 指令 (Instructions)

你现在将扮演 **草稿工 (The Drafter)** 的角色。
**注意：本次任务仅进行调研和可行性分析，不需要提交最终代码，但可以在报告中包含原型代码片段。**

### 🛡️ 沙盒协议 (The Sandbox Protocol)

为了保证生产环境的纯净，执行代码实验时必须遵守：

1. **禁飞区 (No-Fly Zone)**: 严禁在该任务中修改 `src/`、`tests/` 或 `.workflow/` 下的任何文件。
2. **专用靶场 (Target Range)**: 所有的实验代码、临时脚本、数据片段，**必须且只能** 写入 `.sandbox/{topic}/` 目录。
3. **只读生产 (Read-Only Prod)**: 允许 import `src` 中的模块进行调用测试，但**严禁**修改其源码。

### 第 1 步：理解与重述 (Understand & Restate)

分析下方用户的输入。将其重写为一个清晰、简洁的“探针目标 (Spike Goal)”。
**请务必先输出这一句重写后的目标。**

### 第 2 步：沙盒实验 (Sandbox Experiment)

1. 在 `.sandbox/{topic}/` 下创建实验脚本 (e.g., `prototype.py`)。
2. **Run & Log**: 执行脚本，并记录控制台输出或性能数据。
3. **Analysis**: 分析运行结果是否符合预期。

### 第 3 步：记录结果 (Document Results)

根据提供的 `输出模版` 填写调研报告。

- **重要：** 重点在于“能不能做”和“怎么做”，而不是“做好了”。
- 必须包含核心原型代码片段，以便后续实施。

### 输出路径与命名 (Output Config)

**Base Resolution**:

1. Locate the **Workflow Root** directory.
   - It MUST contain `workflow_core/` (folder) AND `ARCH_RULES.md` (file).
2. All output paths below are relative to this `{WorkflowRoot}`.

**Target Path**:
`{WorkflowRoot}/docs/spikes/{topic}/{filename}`

Naming format: `sp_{intent}.md` (example: `sp_websocket-latency.md`)
> **Constraint**: `{topic}` should describe the spike topic, e.g., `network`, `database`.
>
> **Backup Policy (归档策略)**:
> 若目标文件已存在，严禁直接覆盖！
>
> 1. 读取旧内容。
> 2. 将旧文件重命名为 `archive/{filename}.bak_{yyMMdd_HHmm}.md`。
> 3. 写入新文件到原路径。

## 用户输入 (User Input)

{{需要调研的技术点或问题}}

## 任务步骤 (Task Steps)

1. **Research**: 调研相关技术文档、源码或社区方案。
2. **Prototype**: 编写简单的原型代码验证假设 (Label as: `// THROW_AWAY`).
3. **Report**: 生成 `feasibility_report.md` (可行性报告)，包含结论、风险和建议。
    - **注意**: 即使原型代码可以工作，也不要在报告中将其作为最终代码提交。正式落地必须通过 Feature Task。
