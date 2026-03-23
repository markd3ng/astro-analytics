---
name: update-analytics-components-and-docs
overview: 更新并修复项目内已识别为过时或有问题的统计组件实现，补充可执行的验证方案，并新增 `CHANGELOG` 与完整的 `README` 使用/集成文档。默认采用兼容性优先策略，尽量避免破坏现有导出接口。
todos:
  - id: audit-scope
    content: 使用 [subagent:code-explorer] 复核组件与文档差异
    status: completed
  - id: fix-components
    content: 修复 Plausible、Umami、Amplitude、Clarity 组件
    status: completed
    dependencies:
      - audit-scope
  - id: deprecate-minimalanalytics
    content: 处理 MinimalAnalytics 弃用与兼容说明
    status: completed
    dependencies:
      - audit-scope
  - id: update-docs
    content: 新增 CHANGELOG.md 并完善 README 集成文档
    status: completed
    dependencies:
      - fix-components
      - deprecate-minimalanalytics
  - id: validate-output
    content: 执行组件验证并整理检查清单
    status: completed
    dependencies:
      - update-docs
---

## User Requirements

- 更新并修复当前已识别为过时或实现不完整的统计组件，重点覆盖 Plausible、Umami、Amplitude、Clarity，并对无法可靠现代化的 MinimalAnalytics 给出兼容处理与弃用说明。
- 补充一套可执行的验证方案，用于确认各组件输出的脚本地址、关键属性、初始化逻辑与文档示例一致。
- 新增 `CHANGELOG.md`，记录本次修复、兼容策略、文档更新和弃用说明。
- 重写 `README.md`，提供面向 Astro 项目的完整安装、导入、组件用法、集成位置、参数说明、迁移提示与验证步骤。
- 额外评估并输出更优的性能方案，重点关注“尽量不影响首屏加载速度”的集成与加载策略。

## Product Overview

这是一个提供多种网站统计接入组件的组件库。更新后应保证主要统计服务的接入方式更准确、示例更完整、说明更清晰；文档阅读效果上应以 Astro 项目集成为中心，明确推荐在 `src/layouts`、页面模板或全局布局中接入，结构分明、按服务分组、示例统一，便于直接复制集成。

## Core Features

- 修复过时脚本地址与不完整初始化代码
- 保持现有导出入口稳定，优先做兼容式更新
- 为不可可靠修复的组件提供明确弃用与替代说明
- 提供完整接入文档、迁移说明与验证清单

## Tech Stack Selection

- 项目类型：现有组件库
- 当前技术栈：Astro 组件文件（`src/*.astro`）+ TypeScript 前置脚本
- 导出方式：根目录 `index.ts` 统一导出组件
- 文档载体：`README.md`
- 版本与配置：`package.json` 当前版本 `2.7.0`，`tsconfig.json` 继承 `astro/tsconfigs/base`
- 依赖现状：`package.json` 与 `package-lock.json` 均显示当前仓库没有额外运行时依赖或脚本配置

## Implementation Approach

采用“最小改动、兼容优先、文档同步”的修复策略：直接修正明显过时的脚本地址和初始化片段，保留现有导出面不变；对确实无法可靠现代化且会误导用户的能力，以弃用说明替代伪修复。

关键决策：

- `Plausible.astro`：仅修正默认脚本地址，保留 `src` 覆盖能力，避免破坏已有自托管接入。
- `Umami.astro`：兼容现有实现与文档期望的参数命名，内部统一生成正确脚本地址和 `data-website-id`，避免现有用户直接断裂。
- `Amplitude.astro`：升级到官方较新的 Browser SDK 2 接入方式，但继续保留 `apiKey` 作为公开参数，降低迁移成本。
- `Clarity.astro`：改为官方标准 bootstrap 片段，并使用 Astro 变量注入，修复当前 `id` 在脚本中不可用的问题。
- `MinimalAnalytics.astro`：当前实现依赖已废弃的 Universal Analytics `collect` 端点，不做伪现代化；保留导出兼容，同时在 README 与 CHANGELOG 中明确弃用、限制和替代建议。

性能与可靠性：

- 所有组件仍为 O(1) 模板输出，无额外状态管理。
- 继续优先使用 `async` 或 `defer` 的外部脚本加载，避免阻塞页面渲染。
- README 中补充面向 Astro 的推荐集成位置与加载时机建议，区分“全站布局注入”和“仅生产环境注入”。
- 额外评估更优加载策略：如仅在 `import.meta.env.PROD` 下渲染、允许自托管脚本地址、延后到首屏稳定后初始化、按需/条件注入，优先选择不破坏统计准确性且对 LCP/主线程影响更小的方案。
- 不新增运行时依赖，避免包体、安装和维护成本上升。
- 文档示例与组件参数同步维护，降低错误集成概率。

## Implementation Notes

- 复用当前每个服务一个 `.astro` 文件的结构，不引入新的抽象层。
- 需要在内联脚本中使用组件参数时，统一采用 Astro 已有的变量注入方式，避免运行时未定义。
- 保持 `index.ts` 导出名称不变，避免影响现有使用方。
- 不做无关重构，不引入测试框架；验证以组件渲染输出核对和文档示例回归为主。
- 对弃用项只做明确文档化与兼容控制，避免误报“已支持”。

## Architecture Design

当前架构是“独立组件 + 单一导出入口 + 单文档说明”：

- `src/*.astro`：每个统计平台一个独立组件，负责输出对应脚本标签和初始化代码
- `index.ts`：统一暴露公共组件 API
- `README.md`：集中提供安装、导入、用法和集成说明
- `CHANGELOG.md`：记录版本级变更与迁移提醒

本次改动继续沿用该结构，不新增新的模块层级。

## Directory Structure

### Directory Structure Summary

本次实现以组件修复和文档补全为主，不扩展新的运行时模块，重点修改现有统计组件与仓库文档。

- `e:/Projects/astro-analytics/src/Plausible.astro`  [MODIFY]  
目的：修正默认脚本地址。
功能：保留 `domain` 与可覆盖 `src` 的能力。
要求：默认地址更新为官方当前脚本，同时保持自定义地址兼容。

- `e:/Projects/astro-analytics/src/Umami.astro`  [MODIFY]  
目的：修复当前参数设计与脚本拼接问题。
功能：输出正确的 Umami 脚本标签和 `data-website-id`。
要求：优先兼容现有参数，并补齐与 README 一致的推荐用法。

- `e:/Projects/astro-analytics/src/Amplitude.astro`  [MODIFY]  
目的：替换过旧的 Browser SDK 1.x 初始化片段。
功能：接入较新的官方浏览器 SDK 启动方式。
要求：保持 `apiKey` 作为公开参数，继续异步加载，避免阻塞。

- `e:/Projects/astro-analytics/src/Clarity.astro`  [MODIFY]  
目的：修复不完整且变量不可用的初始化代码。
功能：输出官方标准 bootstrap 片段。
要求：正确注入项目 ID，保证脚本加载和初始化顺序可靠。

- `e:/Projects/astro-analytics/README.md`  [MODIFY]  
目的：提供完整使用与集成文档。
功能：补充安装、导入、组件示例、参数说明、Astro 推荐集成位置、迁移说明、弃用说明、验证步骤与性能优化建议。
要求：与实际组件实现逐项对齐，示例可直接复制，并明确适用于 Astro 布局/页面的接入方式。

- `e:/Projects/astro-analytics/CHANGELOG.md`  [NEW]  
目的：记录本次修复内容。
功能：总结组件更新、兼容策略、弃用声明和文档增强。
要求：内容可直接作为发布说明使用，条目清晰可追踪。

### Affected but Compatibility-Only

- `e:/Projects/astro-analytics/src/MinimalAnalytics.astro`  
计划：默认不重写实现逻辑。
处理：通过 README 与 CHANGELOG 标注弃用与限制；仅在确有必要时补充最小注释性改动，避免伪支持。

## Agent Extensions

### SubAgent

- **code-explorer**
- Purpose: 复核 `src/*.astro`、`index.ts`、`README.md` 的差异、参数命名和影响范围
- Expected outcome: 输出精确的修改边界、兼容点和文档同步清单，避免遗漏受影响文件