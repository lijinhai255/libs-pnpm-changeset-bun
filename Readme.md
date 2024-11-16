# 技术总结：基于 pnpm workspace 和 Changeset 的 Monorepo 项目管理

## 项目概述

该项目采用 pnpm workspace 管理多个子包，并利用 Changeset 实现版本控制与发布功能。项目包含三个主要模块：

- inspur-hooks: 基于 React 的自定义 Hook 库。
- inspur-ui: 基于 React 的 UI 组件库。
- apps/inspur-storybook: 利用 Storybook 展示 inspur-ui 组件。

## 关键技术点回顾

1. Monorepo 项目结构

- 工具: pnpm workspace
- 优点:
    统一管理依赖，减少重复安装。
    支持包之间的内联依赖，实现内部包联动升级。
    便于团队协作开发多个互相关联的子项目。

```text
libs-pnpm-changeset-bun/
├── apps/
│   └── inspur-storybook/        # Storybook 项目
├── packages/
│   ├── inspur-hooks/            # Hooks 库
│   └── inspur-ui/               # UI 组件库
├── .changeset/                  # Changeset 配置与版本变更记录
├── pnpm-workspace.yaml          # pnpm 工作区配置
├── package.json                 # 根目录项目配置
└── .npmrc                       # npm 配置

```    

## pnpm workspace

- 关键配置文件: pnpm-workspace.yaml

```text
packages:
  - "packages/*"
  - "apps/*"
```

- 核心命令:

```text
    pnpm install: 安装所有依赖，并处理包之间的依赖。
    pnpm run <script> --filter=<package-name>: 针对指定包运行脚本。
    pnpm add <dependency> -w: 在工作区根目录添加依赖。
```

- 优点:

    高效管理多个包，特别是包之间的依赖关系。
    支持子包共享依赖，节省存储空间。

### Changeset: 版本管理与发布

- 功能: 自动生成版本号、变更日志，并支持自动化发布。

- 关键配置:

    初始化 Changeset: pnpm dlx changeset init

    配置文件 .changeset/config.json:

```text
{
  "changelog": false,
  "access": "public",
  "baseBranch": "main",
  "updateInternalDependencies": "patch"
}

```

- 核心命令:

```text
创建变更: pnpm changeset
应用版本: pnpm changeset version
发布包: pnpm changeset publish --access public
```

- 优点:

    自动管理版本变更，保持版本一致性。

    支持多包协同发布，适合 Monorepo 项目。

### 构建与打包工具

- 工具选择:

    Bun: 用于快速构建和本地开发。

    Rollup: 用于生产级打包（packages/inspur-ui 和 packages/inspur-hooks）。
    
- Rollup 配置:

在 `rollup.config.mjs` 中

```js
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import typescript from "@rollup/plugin-typescript";

export default {
  input: "src/index.ts",
  output: [
    { file: "dist/index.js", format: "cjs", sourcemap: true },
    { file: "dist/index.esm.js", format: "esm", sourcemap: true }
  ],
  plugins: [resolve(), commonjs(), typescript()],
};

```

## 总结与收获

- 技术点:

    使用 pnpm workspace 高效管理多包依赖。

    利用 Changeset 自动化版本管理与发布。

    使用 Rollup 构建生产包，结合 Bun 提高开发效率。

    集成 Storybook 提供可视化组件开发与调试环境。
    
- 适用场景:

    适用于需要管理多个包的 Monorepo 项目，特别是组件库和工具库。

- 提升点:

    学习 Scope 包管理与 npm 发布流程。

    掌握 pnpm 和 Changeset 在 Monorepo 项目中的高效协作方式。
    
    完成了一次 Monorepo 项目的从开发到发布的完整流程。
