---
name: software-development-overview
description: 软件开发全流程总览，AI 入口文件
---

# 软件开发全流程

```
01-requirements → 02-design → 03-pre-development → 04-development → 05-testing → 06-delivery
```

贯穿全程：`base.coding-standards`、`base.review-base`、`base.git-workflow`

## 项目结构规范

workspace 根目录下按职责分目录，代码项目不直接放在根目录。以下为示例，实际目录名由项目决定：

```
workspace/
  docs/          ← 所有阶段输出文档
  designs/       ← UI 设计文件
  frontend/      ← 示例：前端代码项目
  backend/       ← 示例：后端代码项目
  android/       ← 示例：Android 代码项目
  ...            ← 其他代码项目
```

## 文档存放规范

| 阶段 | 路径 |
|------|------|
| 01-requirements | `docs/requirements/<name>.md` |
| 02-design | `docs/design/<name>.md` |
| 03-pre-development | `docs/pre-development/<name>.md` |
| 04-development | `docs/modules/<module-name>.md` |
| 05-testing | `docs/testing/<name>.md` |
| 06-delivery | `docs/deployment/<name>.md` |

## 启动流程

- git 初始化：
  - 询问 commit message 使用的语言（默认英文），确认后全程保持
  - 检查是否已有 git 仓库，若没有则执行 `git init`，并提交全部文件
- 模式选择：任务开始前必须询问开发者选择执行模式，收到回复后全程保持，开发者可随时切换

> 请选择执行模式：1（手动，默认）每阶段需确认后推进；2（自动）每阶段完成后自动推进

## 执行规则

- 步骤输出：每个步骤必须先输出标题再输出结果，子步骤逐一执行并输出，输出必须显式，不能跳过或静默
- 阶段门控：每个阶段是一道门，未通过 review 绝对禁止进入下一阶段，所有 review 遵守 `base.review-base`
- 手动模式：收集信息 → 输出产物 → 执行 review → 告知结果并等待开发者明确确认 → 推进下一阶段
- 自动模式：收集信息 → 输出产物 → 执行 review → 告知结果并自动推进下一阶段

## 禁止行为

- 模式未选：禁止在未询问模式选择前开始任何阶段
- 阶段跳跃：禁止跨阶段推进，顺序不可乱，禁止跳过任何步骤
- 阶段合并：禁止将多个阶段合并执行
- 假设确认：手动模式下禁止假设开发者已确认而自动推进
- 自作主张：禁止自行简化、合并、省略或调整任何步骤，严格按文档执行
