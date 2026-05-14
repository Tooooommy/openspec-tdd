# OpenSpec-TDD 技能包 README

> 整合 OpenSpec 规范驱动开发与 Matt Pocock TDD 技能的端到端流水线，专为追求工程质量的开发者设计。

## 📦 概述

OpenSpec-TDD 是一个 **AI 编排技能**，它将 OpenSpec 的规范管理能力与 Matt Pocock 的 TDD 工程纪律无缝串联，形成一条从需求到交付的完整流水线。它不重复实现任何底层功能，只是按正确顺序调用已有工具。

### 核心理念

- **规范即真理**：先写规范，后写代码
- **TDD 强制执行**：每个任务都走红-绿-重构循环
- **原子化变更**：一次只处理一个功能，完成后归档
- **门禁机制**：每个阶段都有检查点，确保质量

## 🚀 安装

### 1. 安装依赖工具

```bash
# 安装 OpenSpec CLI
npm install -g @fission-ai/openspec

# 安装 Matt Pocock Skills（在 Claude Code 中执行）
npx skills@latest add mattpocock/skills

# 在项目根目录初始化 OpenSpec
cd your-project
openspec init
```

### 2. 安装本技能包

将 `.claude/skills/openspec-tdd/` 目录复制到你的项目根目录。最终结构如下：

```
your-project/
├── .claude/
│   └── skills/
│       └── openspec-tdd/
│           ├── SKILL.md
│           └── references/
│               ├── quality-gates.md
│               ├── tdd-guidelines.md
│               └── review-checklist.md
├── openspec/          # OpenSpec 工作目录
└── ...                # 你的项目代码
```

## 📖 使用指南

### 基本命令

| 命令                                | 作用                               |
| ----------------------------------- | ---------------------------------- |
| `/openspec-tdd start "功能描述"`    | 启动完整 6 阶段流水线              |
| `/openspec-tdd continue`            | 从中断处继续（基于 tasks.md 标记） |
| `/openspec-tdd implement [task-id]` | 跳过前期，直接对指定任务执行 TDD   |
| `/openspec-tdd review`              | 单独执行架构审查                   |
| `/openspec-tdd verify`              | 单独执行验证（不归档）             |
| `/openspec-tdd finish`              | 直接归档（需确保所有任务完成）     |
| `/openspec-tdd status`              | 显示当前进度                       |

### 典型工作流

```bash
# 1. 开始一个新功能
/openspec-tdd start "添加用户邮箱登录"

# AI 会依次执行：
# - 阶段1：需求拷问（调用 /grill-me）
# - 阶段2：生成 OpenSpec 规范（proposal, specs, design, tasks）
# - 阶段3：拆解任务（调用 /to-issues）
# - 阶段4：逐个任务 TDD（调用 /tdd）
# - 阶段5：架构审查（调用 /improve-codebase-architecture）
# - 阶段6：验证并归档

# 2. 中途中断后，继续
/openspec-tdd continue

# 3. 查看进度
/openspec-tdd status

# 4. 单独实现某个任务（如任务3）
/openspec-tdd implement 3

# 5. 手动执行架构审查
/openspec-tdd review

# 6. 最终归档（跳过审查）
/openspec-tdd finish
```

## 🔧 命令详细说明

### `/openspec-tdd start [描述]`

- 启动完整的 6 阶段流程
- 会依次调用 `/grill-me` → `openspec new` → `/to-issues` → `/tdd` → `/improve-codebase-architecture` → `openspec archive`
- 如果中途退出，下次使用 `continue` 继续

### `/openspec-tdd continue`

- 自动检测当前活跃的 change（基于最近的 `tasks.md`）
- 找到第一个未完成的任务（`[ ]`），从该任务继续 TDD
- 若所有任务已完成，则自动进入架构审查阶段

### `/openspec-tdd implement [task-id]`

- 跳过阶段1-3，直接对指定任务执行 TDD
- 需要 `tasks.md` 中存在该任务 ID 且未标记完成
- 任务完成后自动标记 `[x]`

### `/openspec-tdd review`

- 单独执行阶段5：调用 `/improve-codebase-architecture` 生成审查报告
- 可用于任意阶段，评估当前代码质量

### `/openspec-tdd verify`

- 单独执行阶段6的验证部分：`openspec validate --strict` + 测试套件
- 不执行归档，可用于检查规范与代码的一致性

### `/openspec-tdd finish`

- 跳过阶段4-5，直接执行阶段6（验证+归档）
- 警告：请确保所有任务已完成、测试通过，否则可能归档不完整的规范

### `/openspec-tdd status`

- 输出当前 change 名称、阶段、任务完成比例（如 `3/5`）
- 帮助了解进度

## 🗂️ 参考文件说明

技能包附带三个参考文件，存放于 `references/` 目录：

- **`quality-gates.md`**：各阶段的质量门禁清单，用于人工确认
- **`tdd-guidelines.md`**：TDD 实施细则（红-绿-重构详解、垂直切片、反模式）
- **`review-checklist.md`**：架构审查检查清单（接口设计、测试质量、覆盖率、安全性等）

这些文件会被 AI 引用，你也可以直接查阅以理解预期标准。

## ⚠️ 故障排查

| 问题                              | 可能原因                      | 解决方法                                         |
| --------------------------------- | ----------------------------- | ------------------------------------------------ |
| `openspec: command not found`     | OpenSpec CLI 未安装           | `npm install -g @fission-ai/openspec`            |
| `/tdd` 无效                       | Matt Pocock Skills 未添加     | `npx skills@latest add mattpocock/skills/tdd`    |
| `openspec validate --strict` 失败 | spec 格式错误                 | 确保每个需求有 `#### Scenario:`，使用 SHALL/MUST |
| 任务粒度太大                      | `/to-issues` 未调用或拆分不细 | 手动运行 `/to-issues` 重新拆解                   |
| 架构审查报告关键问题过多          | 设计缺陷或测试不足            | 将问题转为新任务，继续 TDD                       |
| 中断后无法恢复                    | `tasks.md` 中 `[x]` 标记丢失  | 手动补充标记，再用 `/openspec-tdd continue`      |
| `implement` 找不到任务            | 任务 ID 格式不匹配            | 检查 `tasks.md` 中是否如 `- [ ] 3. 任务描述`     |

## 📚 最佳实践

- **每次只做一个功能**：一个 change 对应一个完整功能，避免混杂
- **认真对待门禁**：在阶段1/3/5 的人工确认点上，花时间审查规范、任务清单和架构报告
- **善用 `status`**：随时查看进度，避免迷失
- **Commit 规范**：建议将规范文件（`openspec/changes/`）和代码分开提交，便于追溯
- **定期归档**：完成一个 change 后立即归档，保持 `specs/` 与代码同步

## 🤝 贡献与定制

本技能包是开源的，你可以根据需要修改 `SKILL.md` 或参考文件。但请遵循以下原则：

- **不重复造轮子**：优先使用 OpenSpec CLI 和 Matt Pocock Skills 已有功能
- **保持编排角色**：技能只负责顺序调用，不实现新逻辑
- **门禁可调**：可根据项目严格程度修改 `quality-gates.md` 中的条目

## 📄 许可证

MIT

---

**现在你可以开始使用 `/openspec-tdd start` 体验规范驱动 + 测试驱动的严谨开发流程了！**
