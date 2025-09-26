# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

- 回答必须使用中文
- 在docker中禁止执行删除等操作
- 修改代码时，要考虑对于项目的破坏程度

## 项目概述

这是一个基于 Vue 2 的订阅转换前端项目，使用 Element UI 作为 UI 框架。项目是对 youshandefeiyang/sub-web-modify 的魔改版本，主要特点是去广告、去推广，仅保留本地后端支持，支持多种代理协议格式转换。

## 技术栈

- **前端框架**: Vue 2.7.16
- **UI 库**: Element UI 2.15.14
- **路由**: Vue Router 3.6.5
- **HTTP 客户端**: Axios 1.7.2
- **构建工具**: Vue CLI 5.0
- **样式**: Sass
- **图标**: SVG Sprite (自定义图标系统)

## 常用开发命令

```bash
# 安装依赖
yarn install

# 启动开发服务器
yarn serve

# 构建生产版本
yarn build

# Docker 构建 (多平台)
docker buildx build --platform linux/amd64,linux/arm,linux/arm64 -t sub-web-modify .
```

## 项目架构

### 核心文件结构
```
src/
├── App.vue                 # 根组件
├── main.js                 # 应用入口
├── router/index.js         # 路由配置 (单页应用，仅一个路由)
├── views/Subconverter.vue  # 主要业务组件
├── components/SvgIcon/     # SVG 图标组件
├── plugins/                # 插件配置 (element-ui, axios, clipboard 等)
├── assets/css/             # 样式文件 (支持亮色/暗色主题)
└── icons/                  # SVG 图标资源
```

### 关键特性
- **单页应用**: 只有一个主要路由 `/` 指向 Subconverter.vue
- **主题支持**: 自动跟随系统的暗色/亮色模式
- **SVG 图标系统**: 使用 svg-sprite-loader 处理图标
- **插件化架构**: 通过 plugins 目录管理各种功能插件

### 环境配置
- **默认后端**: `http://localhost:25500` (本地 subconverter)
- **远程配置**: 精简为 `MGX.ini`
- **Docker 支持**: 基于 nginx:1.24-alpine 的多阶段构建

### 构建配置
- 使用 Vue CLI 5.0 构建系统
- babel-plugin-component 按需加载 Element UI 组件
- svg-sprite-loader 处理 SVG 图标
- 支持路径别名 `@/*` 指向 `src/*`

### 部署说明
- 生产构建输出到 `dist/` 目录
- Docker 镜像基于 nginx 1.24-alpine
- 支持 linux/amd64、linux/arm、linux/arm64 多架构
- 通过 GitHub Actions 自动构建和推送 Docker 镜像

## 开发注意事项

- 项目使用 Vue 2.x，注意语法差异
- Element UI 使用按需导入，添加新组件时需在 plugins/element-ui.js 中注册
- SVG 图标放在 src/icons/ 目录，会自动构建为雪碧图
- 样式文件支持 Sass，主题文件在 assets/css/ 目录