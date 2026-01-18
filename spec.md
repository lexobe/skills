---
description: Fractal Spec（分形spec）的管理协议与规则 (specs/spec-*.md).
---

## User Input

```text
$ARGUMENTS
```

You **MUST** consider the user input before proceeding (if not empty).

## Goal

接收用户输入，严格遵循分形架构与命名约束，将其写入相应的 Spec 文件。

## Operating Constraints

**1. 标识与命名 (Identification & Naming)**:
- **文件名即 ID**: 唯一标识符。
- **单单词约束**: Topic 片段 **MUST** 仅为一个英文单词。禁止短语、下划线。
    - *例外*: 通用缩写 (API, CLI) 及字典闭合复合词 (Database) 视为单单词。
    - ✅ `spec-runtime.md`, `spec-runtime-scheduler.md`
    - ❌ `spec-state_machine.md`, `spec-statemachine.md` -> `spec-state-machine.md`
- **层级命名**: 子 Spec 遵循 `spec-<parent>-<child>.md`。

**2. 变更范围 (Modification Scope)**:
- **STRICTLY SPEC-ONLY**: 仅限 `specs/spec-*.md`。
- **解耦**: 严禁同时修改 `plan.md`, `tasks.md` 或代码。

**3. 内容结构 (Structure)**:
Spec 文件 **MUST** 遵循以下模板。不适用章节保留标题并注 "N/A"。

    ```markdown
    # Spec: [Topic Name]

    ## 1. Scope & Goals
    - **Scope**: 核心问题与职责。
    - **Non-Goals**: 边界与反目标。
    - **Context**: 架构位置。

    ## 2. Terms & Concepts
    - **Glossary**: 术语表。
    - **Core Model**: 核心模型/图示。
    - **Composition**: 子 Spec 链接。

    ## 3. Invariants & Rules
    - **Invariants**: 系统公理 (Always True)。
    - **Normative Constraints**: MUST/SHOULD 约束。

    ## 4. Interfaces & Contracts
    - **Inputs/Outputs**: CLI/API/IO。
    - **Protocols**: Schema/Format。

    ## 5. Behaviors & Errors
    - **Flows**: 流程与状态。
    - **Errors**: 错误处理。

    ## 6. Verification
    - **Test Strategy**: 验证策略。
    - **Test Anchors**: 测试文件链接。
    - **Conformance Criteria**: 验收标准。
    ```

**4. 子 Spec 拆分协议 (Splitting Protocol)**:
- **Trigger**: 文件 > 300 行 或 逻辑模块独立。
- **Action**:
    1. **Create**: 按命名约束创建子文件 (并明确 Context)。
    2. **Migrate**: 迁移细节 (Rules, Interfaces, Behaviors) 至子文件。
    3. **Abstract**: 父文件保留摘要与 Invariants。
    4. **Link**: 父文件 `2. Terms & Concepts` 添加链接。

**5. 引用**:
- 使用 `specs/spec-*.md#anchor`。

## Execution Steps

### 1. 理解与规划 (Understand & Plan)
- 理解输入。
- **定位**: 锁定现有文件。
  - *(例外: 若新主题独立且无法归属现有 Spec，允许创建新的一级 Spec)*

### 2. 执行修改 (Modify)
- 按标准结构写入目标文件。

### 3. 分析 (Analyze)
- 检查长度 (>300行?) 与逻辑密度。

### 4. 决策 (Decide)
- 是否拆分?

### 5. 执行拆分 (Execute Split)
- **创建**: 提取单单词 Topic，创建 `<current>-<new_topic>.md`。
- **迁移**: 移动详细内容。
- **保留**: 父文件保留摘要。
- **链接**: 父子双向确认 (父加链接，子确认 Context)。
- **更新引用**: 修复全局死链。
