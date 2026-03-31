---
alwaysApply: true
---
# uni-app x (uvue + uts) 分层解耦与开发规约

## 1. 项目结构与分层解耦规范

**强制** 遵循以下 uni-app x 标准目录结构，强调逻辑与视图的彻底分离：

```
src/
├── pages/            # uvue 页面组件目录（视图层）
├── components/       # 可复用 uvue UI组件（视图层）
├── static/           # 静态资源（图片、字体等，小程序要求必须放在此目录）
├── store/            # 状态管理（推荐 uts 共享状态或 Pinia）
├── services/         # API服务层（.uts，逻辑层）
├── utils/            # 业务逻辑与工具函数（.uts，逻辑层）
├── types/            # UTS/TypeScript 类型定义
├── App.uvue          # 根组件，配置应用级生命周期
├── main.uts          # 入口文件
├── manifest.json     # 应用配置（AppID、权限等）
└── pages.json        # 页面路由与全局外观配置
```

**正例**：将数据获取与复杂处理放在 `utils/user.uts` 中，在 `pages/index/index.uvue` 中仅负责渲染和事件绑定。
**反例**：在 `.uvue` 文件的 `<script>` 中直接编写长篇的复杂算法、深层数据处理或混合网络请求。

---

## 2. 视图层 (uvue) 规范

**强制** 页面和组件必须使用 `.uvue` 后缀。
**强制** 采用 Vue 3 `<script setup lang="uts">` 语法，并严格标注类型。

```vue
<template>
  <view class="user-profile">
    <text class="title">{{ user.name }}</text>
    <text class="email">{{ user.email }}</text>
  </view>
</template>

<script setup lang="uts">
import { onMounted, ref } from 'vue'
import { onLoad } from '@dcloudio/uni-app'
import { fetchUserInfo, type UserInfo } from '@/services/user.uts'

// 仅保留与视图直接相关的数据和状态
const props = defineProps<{ userId: number }>()
const user = ref<UserInfo>({ name: '', email: '' } as UserInfo)

// 页面级生命周期（仅在 pages 下生效）
onLoad((options) => {
  console.log('页面参数：', options)
})

onMounted(async () => {
  // 调用抽离在 services/uts 中的业务逻辑
  user.value = await fetchUserInfo(props.userId)
})
</script>

<style>
/* uvue 默认支持 flex 布局，App端默认具有样式隔离特性 */
.user-profile {
  flex-direction: column;
  padding: 40rpx;
  border: 2rpx solid #eee;
}
</style>
```

**注意**：
- 必须抛弃 `div`、`span`、`p`、`img` 等 Web 标签，全面使用 `view`、`text`、`image`、`scroll-view` 等内置原生组件。
- `.uvue` 的 `<style>` 中不支持传统的 CSS 浮动 (`float`) 和复杂的组合选择器，必须拥抱纯 Flex 布局。

---

## 3. 逻辑层 (uts) 规范

**强制** 业务逻辑、网络请求、数据处理必须使用 `.uts` (uni type script) 编写，实现与视图层的彻底解耦。
**强制** `uts` 是一门强类型语言，变量和函数参数必须显式声明类型，避免使用 `any`。

```typescript
// src/services/user.uts
export type UserInfo = {
  id: number
  name: string
  email: string
}

/**
 * 将网络请求和数据格式化逻辑抽离到 uts 文件
 */
export async function fetchUserInfo(userId: number): Promise<UserInfo> {
  return new Promise((resolve, reject) => {
    uni.request({
      url: `/api/v1/users/${userId}`,
      method: 'GET',
      success: (res) => {
        if (res.statusCode >= 200 && res.statusCode < 300) {
          // uts 中需要进行严格的类型断言
          const data = res.data as UserInfo
          resolve(data)
        } else {
          uni.showToast({ title: '请求失败', icon: 'none' })
          reject(new Error('Request failed'))
        }
      },
      fail: (err) => {
        uni.showToast({ title: '网络错误', icon: 'none' })
        reject(err)
      }
    })
  })
}
```

---

## 4. 路由配置规范

**强制** 不使用 `vue-router`，小程序的路由必须在 `src/pages.json` 中配置。
**强制** 页面跳转使用 uni-app 原生 API（如 `uni.navigateTo`, `uni.switchTab`, `uni.redirectTo` 等）。

---

## 5. 性能与跨端优化

**强制** 逻辑代码在 `uts` 中编写，确保能被原生编译器（Kotlin/Swift）高效编译执行，极大提升 App 端运行效率。
**强制** 合理规划组件颗粒度，避免单个 `.uvue` 文件节点过深或频繁触发视图层重绘。
**强制** 列表渲染优先考虑使用原生的 `list-view` (App端) 或优化后的 `scroll-view`，避免使用简单的 `view` 循环。

---

## 核心价值

1. **强类型与高性能**：uts 提供严格类型校验并可直接编译为 Android/iOS 原生代码，打破传统 JS 桥接的性能瓶颈。
2. **彻底解耦**：视图层 (uvue) 仅负责 UI 渲染，逻辑层 (uts) 负责业务与数据流，使得代码更容易测试和跨端复用。
3. **原生体验**：通过 uvue 的原生渲染引擎，使应用在 App 端获得媲美原生开发的流畅体验，同时完美编译至小程序端。