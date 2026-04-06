# 中国古建筑智慧学习平台 UI 开发指南 (UI Style Guide)

本文档旨在总结本项目（CN-Building）的视觉风格与交互规范，为后续 AI Agent 或开发人员提供统一的 UI/UX 参考。

## 1. 核心设计理念
- **风格定位**：传统国风 (Traditional Chinese Style) / 宣纸质感。
- **视觉关键词**：古朴、优雅、沉浸、文化底蕴。
- **布局模式**：故事化叙述 + 功能丰富型 (Storytelling + Feature-Rich)。

## 2. 色彩体系 (Color Palette)

| 角色 | 十六进制代码 | 视觉描述 |
| :--- | :--- | :--- |
| **主背景 (Background)** | `#F6EFE9` | 宣纸暖米色，营造古籍质感 |
| **主文字 (Text Primary)** | `#4A2E1B` | 深木色，用于标题和核心文字 |
| **次要文字 (Text Secondary)** | `#6D4C41` | 棕褐色，用于描述信息 |
| **核心装饰 (Gold/Accent)** | `#C6A664` | 古铜金，用于边框、徽章、高亮 |
| **辅助装饰 (Bronze)** | `#8C5A2B` | 沉稳棕，用于深色边框或强调 |
| **纯白容器 (White)** | `#FFFFFF` | 用于部分卡片背景，增强对比 |

## 3. 组件规范

### 3.1 头部徽章 (Hero Badge)
- **结构**：外层金色边框 + 内层深木色背景。
- **样式**：
  ```css
  .hero-badge { background-color: #C6A664; padding: 4px; border-radius: 8px; }
  .hero-badge-inner { background-color: #4A2E1B; border: 1px solid #E8C382; }
  .hero-title { color: #FFF3E0; letter-spacing: 4px; font-weight: bold; }
  ```

### 3.2 底部抽屉 (Bottom Drawer)
- **组件**：`components/BottomDrawer/BottomDrawer.uvue`
- **规范**：
  - 顶部必须带有金色横向边框 (`2px solid #C6A664`)。
  - 必须包含拖动手柄 (`drag-bar`)，颜色为 `#D2B48C`。
  - 支持手势拖拽吸附（min/max height）。

### 3.3 卡片与列表 (Cards & Lists)
- **卡片**：宽度通常为 `46%` (双栏) 或 `100%` (单栏)。
- **边框**：统一使用 `#C6A664` (1px) 或更深的 `#8C5A2B` (2px)。
- **背景**：卡片内部通常使用 `#F6EFE9` 以区别于纯白容器。

### 3.4 按钮 (Buttons)
- **主按钮**：深木色背景 (`#4A2E1B`)，浅色文字，通常带有金色细边框。
- **圆角**：统一使用较大的圆角（20px+）或符合古建筑线条的方圆结合。

## 4. 间距与排版 (Typography & Spacing)

- **全局边距**：容器内边距统一为 `20px`。
- **标题间距**：标题文字推荐使用 `letter-spacing: 4px` 增加呼吸感。
- **正文行高**：正文描述文字行高推荐为 `1.6` - `1.8`，模拟古籍阅读体验。
- **分界线**：使用金色细线 (`1px solid #C6A664`) 作为章节区分。

## 5. 交互规范
- **点击反馈**：所有交互元素必须配置 `hover-class`，推荐颜色为 `#E8DCC8` (浅褐色)。
- **过渡动画**：抽屉和弹窗应平滑过渡，避免瞬间跳变。
- **加载状态**：使用 `components/DataLoading/DataLoading.uvue`，背景色应与全局背景一致。

## 6. 文件参考
- **首页实现**：`pages/home/index.uvue`
- **地图页交互**：`pages/map/index.uvue`
- **通用抽屉**：`components/BottomDrawer/BottomDrawer.uvue`

---
*请在开发新功能时严格遵守上述规范，确保平台视觉风格的高度统一。*
