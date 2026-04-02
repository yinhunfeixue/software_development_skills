---
name: git-workflow
description: Git 分支管理与版本号规范，coding 每个功能必须遵守
---

# Git 工作流

## 分支规范
- 功能分支：每个功能新建独立分支 `feature/<功能名>`，bugfix 用 `fix/<描述>`
- 合并时机：开发完成，经 review 通过后 merge 到主分支
- 分支清理：merge 后保留功能分支，不删除

## 并行开发（Worktree）

子代理并行开发时，用 git worktree 隔离工作区：

- 创建：`git worktree add ../worktrees/<功能名> -b feature/<功能名>`
- 隔离：每个子代理独占一个 worktree，禁止共用
- 清理：合并后 `git worktree remove ../worktrees/<功能名>`

## 版本号
遵循 semver：`MAJOR.MINOR.PATCH`

| 级别 | 场景 |
|------|------|
| PATCH | bugfix |
| MINOR | 新功能，向下兼容 |
| MAJOR | 破坏性变更 |

- 打 tag 时机：每次 merge 到主分支后执行 `git tag v1.2.3`

## Commit Message
格式：`<type>: <描述>`

| type | 用途 |
|------|------|
| feat | 新功能 |
| fix | bug 修复 |
| refactor | 重构（不影响功能） |
| chore | 构建/依赖/配置变更 |
| docs | 文档更新 |
| review | review 修改 |
