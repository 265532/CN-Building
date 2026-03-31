---
alwaysApply: false
description: 小程序（微信/多端）特定开发与功能限制规约
---

# 小程序特定开发规约

## 1. 权限与隐私保护

**强制** 小程序中获取用户隐私信息（如手机号、地理位置、相册、麦克风等），必须提前在 `app.json` / `manifest.json` 中声明对应的权限。
**强制** 调用隐私接口前，必须确保用户已经同意了《小程序隐私保护指引》（使用 `wx.requirePrivacyAuthorize` 或通过页面弹窗引导用户同意）。

### 1.1 登录与授权
- **静默登录**：使用 `uni.login` 获取 `code`，发往后端换取 `openid`，不可直接依赖前端获取敏感信息。
- **获取手机号**：必须通过 `<button open-type="getPhoneNumber" @getphonenumber="onGetPhoneNumber">` 组件触发，由用户主动点击，不能在页面加载时强制弹窗。
- **获取头像昵称**：必须使用 `<button open-type="chooseAvatar">` 和 `<input type="nickname">` 进行收集，不再支持直接通过 `wx.getUserProfile` 获取真实头像昵称。

## 2. 存储与缓存限制

**强制** 了解并遵循小程序的本地存储限制。
- 单个 key 允许存储的最大数据长度为 1MB。
- 所有数据存储上限为 10MB。
- 不要将大量图片（Base64）或视频缓存在本地 `Storage` 中。

## 3. UI 与组件限制

**强制** 小程序中无法使用部分 Web 特有的 DOM API（如 `document.getElementById`, `window` 对象）。
**强制** `z-index` 的层级管理需注意，原生组件（如 `map`, `video`, `canvas`, `textarea`）在旧版基础库中层级最高，需要使用 `cover-view` 和 `cover-image` 进行覆盖，或者开启 `同层渲染` 特性。

## 4. 网络请求规范

**强制** 小程序的网络请求必须使用 HTTPS。
**强制** 所有请求的域名（包括接口、图片下载、Web-view 加载），必须在微信公众平台的**开发设置 -> 服务器域名**中提前配置合法域名。
- 在开发阶段，可以在微信开发者工具中勾选“不校验合法域名、web-view（业务域名）、TLS版本以及HTTPS证书”以方便本地调试。但在正式上线前必须配置完毕。

## 5. 性能调优指标与 uts 编译

- **首屏加载**：尽量控制在 1.5s 以内。
- **包体积**：
  - 整个小程序所有分包大小不超过 20MB。
  - 单个分包/主包大小不能超过 2MB。
- **setData 频次**：不要频繁调用引发视图更新的方法，合并数据更新，避免一次性传递大量数据（单次不能超过 256KB）。
- **uts 编译注意事项**：
  - 在小程序端，`.uts` 代码会被编译为原生的 `JavaScript`。
  - 虽然 `uts` 是强类型，但在编译到 JS 后会丢失类型检查，因此在逻辑层必须利用 HBuilderX 的强类型推导确保数据正确性。
  - 尽量不要在 `uts` 中使用仅限 App 端的原生 API (如直接调用 iOS/Android 的系统包)，在多端复用时需加上条件编译（`#ifdef APP` ... `#endif`）。
