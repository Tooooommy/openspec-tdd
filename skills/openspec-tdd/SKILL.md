---
name: openspec-tdd
description: 整合 OpenSpec 规范驱动开发（SDD）与 Matt Pocock TDD 技能的端到端流水线。当用户要求“用 SDD+TDD 开发功能”、“按照规范驱动加测试驱动完成需求”或提及“openspec + tdd”时使用。触发命令：/openspec-tdd
license: MIT
compatibility: 需要 OpenSpec CLI 和 mattpocock/skills 已安装
version: 1.0.0
---

# OpenSpec-TDD：规范先行 + 测试驱动开发流水线

## 概述

本技能是一个**编排器**，将 OpenSpec 的规范管理能力和 Matt Pocock 的工程纪律技能组合成一条自动化流水线。  
它不重复实现任何底层功能，而是按顺序调用已有工具：

- **OpenSpec CLI** → 变更提案、规范验证、归档
- **Matt Pocock Skills** → `/grill-me`（需求拷问）、`/to-issues`（任务拆解）、`/tdd`（红-绿-重构）、`/improve-codebase-architecture`（架构审查）

## 前置条件

1. 已全局安装 OpenSpec CLI：`npm install -g @fission-ai/openspec`
2. 已在当前项目中初始化 OpenSpec：`openspec init`
3. 已添加 Matt Pocock Skills：`npx skills@latest add mattpocock/skills`
4. AI 工具支持 Skills 加载和斜杠命令

## 核心原则

- **规范即真理**：任何功能必须先有 OpenSpec 规范，再写代码
- **TDD 强制执行**：每个实现任务必须通过 `/tdd` 完成，遵循红-绿-重构
- **原子化变更**：一次只处理一个 OpenSpec change，完成后归档
- **门禁机制**：每个阶段都有明确的检查点，未通过不能进入下一阶段

---

## 工作流（6 个阶段）

### 阶段 0：触发

用户输入 `/openspec-tdd` 或 `/openspec-tdd start [功能描述]`

### 阶段 1：需求对齐（调用 `/grill-me`）

1. 自动调用 `/grill-me` 技能，对需求进行结构化拷问
2. 收集所有澄清结果，写入 `.scratch/openspec-tdd/requirements.md`
3. **门禁**：必须完成所有 `/grill-me` 问题，用户确认需求无歧义

### 阶段 2：规范生成（调用 OpenSpec CLI）

1. 创建变更目录：`openspec new <change-name>`（名称从需求摘要自动生成）
2. 将需求澄清结果写入 `proposal.md`（背景/目标/范围/约束）
3. 生成技术规范：`openspec instructions specs --json` → 编写 `specs/*.md`
   - 每个需求使用 `### Requirement:`，每个场景使用 `#### Scenario:`（GIVEN/WHEN/THEN）
4. 生成设计文档：`openspec instructions design --json` → 编写 `design.md`
5. 生成初始任务清单：`openspec instructions tasks --json` → 编写 `tasks.md`
6. **门禁**：运行 `openspec validate <change-name> --strict`，必须通过

### 阶段 3：任务拆解（调用 `/to-issues`）

1. 调用 `/to-issues` 技能，将 `tasks.md` 中的每个任务按垂直切片拆解
2. 确保每个任务可在 5–10 分钟内完成一个 TDD 循环
3. 更新 `tasks.md` 为细化后的版本
4. **门禁**：用户确认任务拆解合理

### 阶段 4：TDD 实施（循环调用 `/tdd`）

对于 `tasks.md` 中的**每个未完成任务**：

1. 选择任务，在 AI 对话中调用：`/tdd 实现任务: <任务描述>`
   - AI 自动执行 RED → GREEN → REFACTOR
2. 等待 `/tdd` 完成，确认所有测试通过
3. 在 `tasks.md` 中将该任务标记为 `[x]`
4. 重复直到所有任务完成
5. **门禁**：所有任务完成且测试套件全绿

> 若某个任务多次 TDD 失败，暂停流程并请求用户介入。

### 阶段 5：架构审查（调用 `/improve-codebase-architecture`）

1. 调用 `/improve-codebase-architecture` 技能
2. 获取架构审查报告（接口设计、测试质量、覆盖率、架构一致性）
3. 如果发现问题：
   - 将修复项作为新任务追加到 `tasks.md`
   - 返回阶段 4，对新任务执行 TDD
   - 重复直到审查通过
4. **门禁**：架构审查无关键问题，核心逻辑测试覆盖率 ≥ 80%

### 阶段 6：验证与归档（调用 OpenSpec CLI）

1. 最终验证：`openspec validate <change-name> --strict`
2. 运行完整测试套件（如 `npm test`）
3. 归档变更：`openspec archive <change-name> --yes`
   - 合并规范到 `specs/`，移动变更到 `changes/archive/`
4. 输出完成报告

---

## 命令参考

| 用户命令                            | 作用                               |
| ----------------------------------- | ---------------------------------- |
| `/openspec-tdd start [描述]`        | 启动完整流水线                     |
| `/openspec-tdd continue`            | 从上次中断处继续                   |
| `/openspec-tdd implement [task-id]` | 单独对某个任务执行 TDD（手动模式） |
| `/openspec-tdd review`              | 仅执行架构审查                     |
| `/openspec-tdd verify`              | 仅执行验证（不归档）               |
| `/openspec-tdd finish`              | 执行最终验证并归档                 |
| `/openspec-tdd status`              | 显示当前进度                       |

---

## 示例会话（Claude Code）

```
用户: /openspec-tdd start 添加用户邮箱登录功能

AI: [加载 openspec-tdd 技能]
    🔍 阶段1：需求对齐（调用 /grill-me）
    ❓ 登录凭证：邮箱+密码，是否支持记住我？
    ❓ 失败次数限制？是否需要锁定账号？
    ...（问答循环）

用户: （回答所有问题）

AI: ✅ 需求已对齐，生成 proposal.md...
    📝 阶段2：规范生成
    ✅ openspec validate add-email-login --strict 通过

    📋 阶段3：任务拆解（调用 /to-issues）
    已将初始 tasks.md 拆解为 5 个原子任务：
    1. [ ] 编写用户邮箱格式验证器
    2. [ ] 实现密码哈希比对函数
    3. [ ] 创建登录 API 端点
    4. [ ] 添加登录失败计数
    5. [ ] 编写集成测试

    🔁 阶段4：TDD 实施
    开始任务1：/tdd 实现邮箱格式验证器
    🔴 RED：编写测试用例 test_email_validator
    🟢 GREEN：实现验证器
    🔵 REFACTOR：提取正则常量
    ✅ 任务1完成，测试通过

    是否继续任务2？ (输入继续 或 /openspec-tdd continue)

用户: 继续

AI: 任务2：/tdd 实现密码哈希比对...
    ...（重复直到所有任务完成）

    🏗️ 阶段5：架构审查（/improve-codebase-architecture）
    ✅ 无关键问题，覆盖率 85%

    📦 阶段6：验证与归档
    openspec validate --strict 通过
    npm test 通过
    openspec archive add-email-login --yes
    ✅ 变更已归档，规范已更新。

    用户登录功能交付完成！
```

---

## 故障排查

| 现象                          | 可能原因                                  | 解决方法                                                       |
| ----------------------------- | ----------------------------------------- | -------------------------------------------------------------- |
| `openspec: command not found` | OpenSpec CLI 未安装                       | `npm install -g @fission-ai/openspec`                          |
| `/tdd` 无效                   | mattpocock/skills 未添加                  | `npx skills@latest add mattpocock/skills/tdd`                  |
| 规范验证失败                  | specs 缺少 `#### Scenario:` 或 SHALL/MUST | 按 OpenSpec 要求补充场景描述                                   |
| 任务粒度太大                  | `/to-issues` 未被调用                     | 手动运行 `/to-issues` 重新拆解                                 |
| 架构审查失败                  | 设计缺陷或测试不足                        | 将审查问题转为新任务，继续 TDD                                 |
| 中断后无法继续                | 未保存状态                                | 手动检查 `tasks.md` 完成标记，用 `/openspec-tdd continue` 恢复 |

---

## 扩展与定制

- 若需要更严格的门禁，可在 `references/quality-gates.md` 中定义自定义规则
- 若需要调整 TDD 循环细节，可修改 `/tdd` 技能的配置
- 本技能不修改 OpenSpec 或 Matt Pocock Skills 的任何原始行为，仅编排调用

**就这样开始你的 SDD+TDD 流水线吧！**
