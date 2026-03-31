# 非遗建筑学习平台 (uni-app x 迁移版)

本项目正在从传统 Vue 项目逐步迁移至 **uni-app x** 架构，全面拥抱 `uvue` 和 `uts` (uni type script)。通过分层解耦，实现逻辑层与视图层的分离，最终达成一套代码多端高性能原生运行（Android/iOS/小程序/Web）的目标。

## 技术栈
- **核心框架**: uni-app x (基于 Vue 3 Composition API 范式)
- **视图层**: uvue (`.uvue` 文件，采用原生渲染引擎，仅支持 Flex 布局)
- **逻辑层**: uts (`.uts` 文件，强类型，可编译为 Kotlin/Swift/JS)
- **状态管理**: 推荐使用基于 uts 的共享状态或兼容版 Pinia
- **构建工具**: HBuilderX (内置 uni-app x 编译器)

## 分层解耦与迁移策略 (Vue -> uvue + uts)

在迁移过程中，请遵循以下**分层解耦**原则：
1. **API 与网络层抽离**：将所有接口请求从组件中完全抽离，使用 `uts` 编写独立的 `service` 或 `api` 模块，使用 `uni.request` 封装请求。
2. **业务逻辑层抽离**：将复杂的业务逻辑（数据处理、验证、格式化、本地存储操作等）从 `.vue`/`.uvue` 文件的 `<script>` 中剥离，写入独立的 `.uts` 工具类文件，并通过 `export` 暴露纯函数给视图层调用。
3. **视图层改造 (uvue)**：
   - 将 `.vue` 页面逐步改写为 `.uvue` 格式。
   - 替换 Web 标签：全面抛弃 `div`, `span`, `p`, `img`，改用 `view`, `text`, `image`, `scroll-view`。
   - 样式重构：全面拥抱纯 Flex 布局，移除不支持的 CSS（如 `float`、部分选择器等），确保在原生渲染下表现一致。

## 开发环境建议
- **强制要求**: 使用 [HBuilderX 4.0 及以上版本](https://www.dcloud.io/hbuilderx.html) 进行开发。
- 因为 `uvue` 和 `uts` 具备强类型推导与多端原生编译特性，目前 HBuilderX 提供了最完善的语法提示、类型校验和原生 App 编译支持。

## 运行与编译
在 HBuilderX 中：
- **运行到小程序**：点击运行 -> 运行到小程序模拟器 -> 微信开发者工具。`uts` 代码将被自动编译为原生 `JavaScript`。
- **运行到 App**：点击运行 -> 运行到手机或模拟器。`uts` 代码将被编译为 `Kotlin` (Android) 或 `Swift` (iOS)，`.uvue` 将使用原生渲染引擎，体验媲美纯原生应用。

## 项目规约
请务必参考 `.trae/rules/` 目录下的相关规约进行解耦与重构：
- [uvue UI 设计与样式限制规约](.trae/rules/ui.md)
- [uni-app x (uvue+uts) 开发规约](.trae/rules/vue-project-rule.md)
- [小程序特有特性规约](.trae/rules/miniprogram-rule.md)