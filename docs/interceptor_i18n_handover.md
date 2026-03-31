# 接口拦截器与多语言交接文档

## 模块说明
为全应用提供统一的网络请求入口，统一处理 Token 注入、401 鉴权过期自动跳转以及多语言 (i18n) 请求头的注入。

## 已实现功能
- `utils/request.uts`：封装了全局的 `request` 方法。
- **请求拦截**：在每个请求发起前，自动从 `store/user.uts` 获取 Token 并注入 `Authorization: Bearer` 请求头。
- **多语言拦截**：自动从本地缓存获取当前语言（如 `zh-CN`, `en-US`），并注入 `Accept-Language` 请求头。
- **响应拦截**：针对 HTTP 401 状态码，自动触发 `logout()` 清除本地状态，并跳转至 `/pages/auth/login` 页面重新登录。

## 待完善清单 (Todo)
- [ ] 将目前 `services/` 目录下的所有 `uni.request` 替换为使用 `utils/request.uts` 中封装的全局方法。
- [ ] 在应用启动时 (`App.uvue` 中) 初始化 i18n 配置，并结合 `vue-i18n` 实现全量静态文案的多语言切换。
- [ ] 增加全局的 Loading 态配置与统一的错误 Toast 提示拦截。
