---
name: project-structure
description: 项目结构与文档存放规范
---

# 项目结构规范

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
| 需求分析 | `docs/requirements/index.md`（入口）+ `docs/requirements/<需求名>.md`（每个需求单独一个文件） |
| 软件设计 | `docs/design/index.md`（入口）+ `docs/design/<模块名>.md`（每个模块单独一个文件） |
| 开发规划和准备 | `docs/dev-planning/<name>.md` |
| 开发 | `docs/modules/<module-name>.md` |
| 测试 | `docs/testing/<name>.md` |
| 部署 | `docs/deployment/<name>.md` |
