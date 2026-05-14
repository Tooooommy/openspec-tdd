# OpenSpec-TDD 完整技能包

本技能包将 **OpenSpec 规范驱动开发 (SDD)** 与 **Matt Pocock's TDD 技能** 深度整合，提供一条从需求到归档的、具有严格质量门禁的自动化流水线。所有底层能力均复用现有工具，不重复造轮子。

## 📁 目录结构

```
.claude/skills/openspec-tdd/
├── SKILL.md                    # 主技能文件（必选）
└── references/                 # 参考文件（可选，但强烈推荐）
    ├── quality-gates.md        # 各阶段质量门禁详细定义
    ├── tdd-guidelines.md       # TDD 实施补充细则
    └── review-checklist.md     # 架构审查检查清单
```

---

## 1. 主技能文件：SKILL.md

```markdown
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

| 用户命令 | 作用 |
|---------|------|
| `/openspec-tdd start [描述]` | 启动完整流水线 |
| `/openspec-tdd continue` | 从上次中断处继续 |
| `/openspec-tdd implement [task-id]` | 单独对某个任务执行 TDD（手动模式） |
| `/openspec-tdd review` | 仅执行架构审查 |
| `/openspec-tdd verify` | 仅执行验证（不归档） |
| `/openspec-tdd finish` | 执行最终验证并归档 |
| `/openspec-tdd status` | 显示当前进度 |

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

| 现象 | 可能原因 | 解决方法 |
|------|----------|----------|
| `openspec: command not found` | OpenSpec CLI 未安装 | `npm install -g @fission-ai/openspec` |
| `/tdd` 无效 | mattpocock/skills 未添加 | `npx skills@latest add mattpocock/skills/tdd` |
| 规范验证失败 | specs 缺少 `#### Scenario:` 或 SHALL/MUST | 按 OpenSpec 要求补充场景描述 |
| 任务粒度太大 | `/to-issues` 未被调用 | 手动运行 `/to-issues` 重新拆解 |
| 架构审查失败 | 设计缺陷或测试不足 | 将审查问题转为新任务，继续 TDD |
| 中断后无法继续 | 未保存状态 | 手动检查 `tasks.md` 完成标记，用 `/openspec-tdd continue` 恢复 |

---

## 扩展与定制

- 若需要更严格的门禁，可在 `references/quality-gates.md` 中定义自定义规则
- 若需要调整 TDD 循环细节，可修改 `/tdd` 技能的配置
- 本技能不修改 OpenSpec 或 Matt Pocock Skills 的任何原始行为，仅编排调用

**就这样开始你的 SDD+TDD 流水线吧！**
```

---

## 2. 参考文件：references/quality-gates.md

```markdown
# 质量门禁清单

本文件定义了 OpenSpec-TDD 流水线中每个阶段必须满足的出口条件。AI 必须在进入下一阶段前验证所有门禁。

## 阶段1：需求对齐出口条件
- [ ] `/grill-me` 的所有问题都得到了明确回答
- [ ] 所有歧义点已消除（无“可能”、“大概”等模糊词汇）
- [ ] 用户已明确确认需求理解正确
- [ ] `.scratch/openspec-tdd/requirements.md` 已生成且内容完整

## 阶段2：规范生成出口条件
- [ ] `proposal.md` 包含「背景」「目标」「范围」「约束」四个部分
- [ ] 每个 `specs/*.md` 中的需求都使用 `### Requirement:` 标题
- [ ] 每个需求至少包含一个 `#### Scenario:`，且场景使用 GIVEN/WHEN/THEN 格式
- [ ] 每个场景使用 SHALL、MUST 等规范性关键词
- [ ] `design.md` 包含架构选型、数据模型、异常处理、风险评估
- [ ] `tasks.md` 已生成（即使粒度较粗）
- [ ] `openspec validate --strict` 退出码为 0，无错误和警告

## 阶段3：任务拆解出口条件
- [ ] 每个任务都可在 5–10 分钟内完成一个完整 TDD 循环
- [ ] 任务之间无循环依赖
- [ ] 每个任务有明确的“完成定义”（例如：测试通过、接口符合规范）
- [ ] 用户已确认任务拆解合理

## 阶段4：TDD 实施出口条件
- [ ] `tasks.md` 中所有任务都已标记 `[x]`
- [ ] 所有测试（单元测试 + 集成测试）通过
- [ ] 测试覆盖率（核心逻辑）≥ 80%（可由 `/improve-codebase-architecture` 后续检查）
- [ ] 没有跳过 TDD 循环（即没有“先实现后补测试”的痕迹）

## 阶段5：架构审查出口条件
- [ ] `/improve-codebase-architecture` 报告无“关键”级别问题
- [ ] 所有“建议”级别问题已评估，明确接受或修复
- [ ] 核心逻辑测试覆盖率 ≥ 80%
- [ ] 接口设计不依赖于实现细节
- [ ] 代码结构与 `design.md` 一致

## 阶段6：最终验证与归档出口条件
- [ ] `openspec validate --strict` 再次通过
- [ ] 完整测试套件（例如 `npm test`）退出码为 0
- [ ] `openspec archive` 成功执行，无错误
- [ ] 变更目录已从 `changes/` 移动到 `changes/archive/`
- [ ] 主 `specs/` 目录已更新
```

---

## 3. 参考文件：references/tdd-guidelines.md

```markdown
# TDD 实施细则

本文档为 `/tdd` 技能提供补充指导，确保其在 OpenSpec-TDD 流程中的行为一致且高质量。

## 红-绿-重构的严格执行

在 OpenSpec-TDD 中，每个任务必须经历完整的 TDD 循环，**禁止**一次性实现多个任务。

### RED 阶段（写一个失败的测试）
- 测试必须只**失败**，不能编译错误（除非语言限制）
- 测试只验证当前任务的行为，不依赖未实现的其他任务
- 测试通过**公共接口**（API、函数签名）进行，不测试私有方法
- 使用项目的测试框架和断言库

### 验证 RED
- 运行测试，确认它确实失败（红色）
- 如果测试意外通过，说明需求已实现或测试有误，必须修改测试

### GREEN 阶段（写最少代码让测试通过）
- 只写刚好让当前测试通过的代码
- 允许硬编码、简单实现，不提前考虑扩展性
- 不实现额外功能（YAGNI 原则）

### 验证 GREEN
- 运行所有测试（不仅当前测试），确认全部通过
- 如果有其他测试失败，修复实现（可能违反了契约）

### REFACTOR 阶段（重构）
- 在测试保护下改进代码结构：消除重复、提高可读性、提取常量/函数
- 不改变外部行为（所有测试仍通过）
- 可同时改进测试代码本身

### 验证 REFACTOR
- 再次运行所有测试，确保重构没有引入错误

## 垂直切片 vs 水平分层

OpenSpec-TDD 推荐**垂直切片**方式：每个任务完成一个从接口到存储的完整小功能，而不是先完成所有数据层再完成所有业务层。

示例（用户登录功能）：
- ✅ 垂直：实现“邮箱格式验证”（从输入验证到返回错误信息）
- ✅ 垂直：实现“密码哈希比对”（从获取请求到调用加密库）
- ❌ 水平：先实现所有数据模型，再实现所有服务层

## 测试命名规范

- 测试名称应描述行为，而非实现：`test_email_validation_rejects_missing_at_symbol`
- 使用 Given-When-Then 结构组织测试代码

## 常见反模式与纠正

| 反模式 | 表现 | 纠正方法 |
|--------|------|----------|
| 跳过 RED | 先写实现代码再补测试 | 删除实现代码，从 RED 开始 |
| 巨型 GREEN | 一次性实现完整功能 | 拆分为多个 TDD 任务，逐个完成 |
| 测试实现细节 | 测试私有方法或 mock 内部状态 | 重构测试，仅通过公共接口验证 |
| 跳过重构 | 功能完成后不清理代码 | 强制在 GREEN 之后执行 REFACTOR |
| 依赖未实现任务 | 测试中 mock 了还不存在的模块 | 先实现被依赖的任务，或使用更简单的测试替身 |
```

---

## 4. 参考文件：references/review-checklist.md

```markdown
# 架构审查检查清单

本清单供 `/improve-codebase-architecture` 技能在审查时参考，也可用于人工审查。

## 接口设计
- [ ] 公开 API 是否清晰、自解释？
- [ ] 是否依赖了内部实现细节？（例如导出私有类型）
- [ ] 是否遵循接口隔离原则（不强迫调用方依赖不需要的方法）？
- [ ] 输入输出是否有合理的验证和错误处理？

## 测试质量
- [ ] 测试是否验证行为而非实现细节？
- [ ] 是否有足够的边界条件测试（空值、异常输入）？
- [ ] 是否有集成测试验证组件协作？
- [ ] 测试是否独立、可重复、速度快？

## 覆盖率
- [ ] 核心业务逻辑覆盖率是否 ≥ 80%？
- [ ] 错误处理分支是否被测试覆盖？
- [ ] 是否有未覆盖的代码需要解释？

## 架构一致性
- [ ] 实现是否遵循 `design.md` 中描述的结构？
- [ ] 模块之间的依赖方向是否合理（无循环依赖）？
- [ ] 是否有违反设计原则的地方（如单一职责、开闭原则）？
- [ ] 是否有不必要的重复代码？

## 可维护性
- [ ] 命名是否一致、有意义？
- [ ] 是否有足够的注释解释“为什么”，而非“怎么做”？
- [ ] 配置、常量是否集中管理？
- [ ] 是否引入了不必要的复杂性？

## 安全性
- [ ] 输入是否经过验证和清理？
- [ ] 敏感数据（密码、密钥）是否被正确保护（不记录日志、不明文存储）？
- [ ] 是否有潜在的注入或权限绕过风险？

## 性能
- [ ] 是否有明显的低效操作（N+1 查询、重复计算）？
- [ ] 是否有必要的缓存或异步处理？

## 问题分级
- **关键**：必须修复才能进入下一阶段（如安全漏洞、严重设计缺陷）
- **建议**：最好修复，但可暂时接受（如小范围重复代码、命名不一致）
- **可选**：由开发者决定是否采纳（如风格偏好）
```

---

## 5. 安装与使用说明

### 步骤 1：安装依赖
```bash
# 安装 OpenSpec CLI
npm install -g @fission-ai/openspec

# 在项目根目录初始化
cd your-project
openspec init

# 安装 Matt Pocock Skills
npx skills@latest add mattpocock/skills
```

### 步骤 2：部署技能包
```bash
# 创建技能目录
mkdir -p .claude/skills/openspec-tdd/references

# 将上述 SKILL.md 和三个 references/*.md 放入对应位置
# 例如：
# .claude/skills/openspec-tdd/SKILL.md
# .claude/skills/openspec-tdd/references/quality-gates.md
# .claude/skills/openspec-tdd/references/tdd-guidelines.md
# .claude/skills/openspec-tdd/references/review-checklist.md
```

### 步骤 3：启动 Claude Code（或支持 Skills 的 AI 工具）
在会话中输入：
```
/openspec-tdd start 你的功能描述
```

### 步骤 4：跟随 AI 引导完成各阶段
- 阶段1：回答 `/grill-me` 的问题
- 阶段2：审查生成的 proposal/specs/design/tasks
- 阶段3：确认任务拆解
- 阶段4：观察 TDD 循环执行（可手动调用 `/openspec-tdd continue` 继续）
- 阶段5：审查架构报告
- 阶段6：等待自动归档

---

## 总结

本技能包提供了：

1. **一个主 SKILL.md**：定义 6 阶段流水线，调用现有工具，包含命令、示例、故障排查
2. **三个参考文件**：质量门禁、TDD 细则、审查清单，确保流程和文档的正确性
3. **完整的安装说明**：可直接部署使用

所有的底层能力都来自 OpenSpec 和 Matt Pocock Skills，没有任何重复造轮子。通过严格的门禁和清晰的编排，实现了 **SDD 确保做正确的事** + **TDD 确保正确地做事** 的完美结合。