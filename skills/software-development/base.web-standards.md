---
name: web-standards
description: web 开发规范，web 项目适用，在 base.coding-standards 基础上补充
---

# web 开发规范

> 通用规范见 `base.coding-standards`

## 默认技术栈

若开发者无特殊要求，默认使用：React + TypeScript + Sass + Vite + Ant Design

## Vite 使用指南

- 创建项目：加上 `--no-immediate` 参数避免询问是否立即启动，例如：`npm create vite@latest <项目名> -- --template <模板名> --no-immediate`

## 资源文件规范（图片等）

- 资源文件：图片等资源存放在 `src/assets/` 目录下，代码中直接引用路径；后期替换只需换文件，无需改代码
- 格式选择：

| 格式 | 适用场景 |
|------|------|
| SVG | 单色/简单图标，需缩放不失真 |
| PNG | 多色、复杂或含透明背景的图片 |

## 文字规范

- 禁用 emoji：禁止在 UI 中使用 emoji 文字
- 数据文字：接口返回的动态文字（用户名、内容、状态等）通过接口加载，不硬编码
- 普通文字：UI 固定文字（按钮、标签、提示语等）直接嵌入代码或本地化文件，不依赖接口

## UI 设计工具

#[[file:base.ui-design.md]]
