# 维护任务 (Maintain Task)

## 上下文注入 (Context Injection)

角色定义: {{CONTENT: /roles/librarian.md}}
输出形式: **一致性修复报告 (Reconciliation Report)**

## 指令 (Instructions)

你现在将扮演 **一致性守护者 (The Consistency Guardian)** 的角色。
**核心原则**: **Code is the Source of Truth**. 你是系统的审计官，只负责维护“存续性文档” (Living Docs)，**严禁**修改“事务性文档” (Transactional Docs)。

### 1. 管辖权 (Scope Definition)

- **✅ In Scope (Living Docs)**:
  - `src/**/MODULE.md` (Module Interface)
  - `docs/system_maps/` (Architecture & Data Maps)
  - `docs/audits/` (Drift Reports)
  - `{WorkflowRoot}/ARCH_RULES.md`
- **❌ Out of Scope (Transactional Docs)**:
  - `docs/features/`, `docs/blueprints/`, `docs/spikes/` 等。
  - **Action**: 忽略它们，或者如果发现严重过时，仅标记 `[DEPRECATED]`，绝不修改内容。

### 2. 执行步骤 (Execution Steps)

#### Step 1: 逆向与审计 (Reverse & Audit)

1. **Source Scan**: 扫描 `src/`，提取最新的 API 签名、数据库 Models 和依赖关系。
2. **Drift Check**: 对比代码与 `{WorkflowRoot}/ARCH_RULES.md`，检测架构红线违规。

#### Step 2: 存续文档同步 (Living Doc Sync)

> **Bootstrap Check**: 如果 `docs/system_maps/` 为空，自动触发“全量生成模式”。

执行以下同步：

##### Level 1: 模块契约 (Module Contracts)

- **Target**: `src/{module}/MODULE.md`
- **Action**: 更新 `Interface` 和 `Change Log`。

##### Level 2: 全局地图 (The Grand Maps)

- **Target**: `docs/system_maps/`
- **Actions**:
  - **Data Map**: 根据 Pydantic/ORM 定义，更新 `data_model.mermaid` (ER Diagram)。
  - **API Catalog**: 聚合所有 MODULE.md 的接口，更新 `api_catalog.md`。

#### Step 3: 生成报告 (Report Generation)

输出一份结构化的 **Drift Report**。

### 3. 输出配置 (Output Config)

**File**: `{WorkflowRoot}/docs/audits/maintenance/mt_maintainese_log-{yyyyMMdd}.md`

**Content Template**:

```markdown
# Consistency Reconciliation Report ({Date})

## 🚨 Critical Drifts (架构偏离)
- [ALERT] `OrderService` violates Layering Rule (Direct DB Access).

## ⚠️ Schema Changes (数据变更)
- [NEW] Table `users` added field `last_login`.
- [MODIFIED] Table `orders` index changed.

## ✅ Synchronization Log (同步记录)
- [UPDATED] `src/user/MODULE.md`
- [REGENERATED] `docs/system_maps/data_model.mermaid`
- [AGGREGATED] `docs/system_maps/api_catalog.md`

## 👻 Ghost Assets (幽灵资产)
- [WARN] `utils/unused.py` appears to be dead code.
```

## 用户输入 (User Input)

{{Scope or Module Name}}
