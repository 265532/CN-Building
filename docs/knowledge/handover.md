# 知识图谱与跨文化对照系统交接文档

## 模块说明
为用户提供系统化的古建筑知识网络，包括建筑等级关联、时空分布、技术演化，以及新增的中西跨文化对照系统。

## 已实现功能
- `services/learn.uts`：重构为 `uni.request` 形式，预留了真实数据接口。新增了“跨文化对照系统”和“游戏式沉浸学习”的路径入口。
- `services/knowledge.uts`：支持根据不同 `type`（如 `crosscultural`, `network`）返回对应的图谱或对照节点详情数据，并处理了网络失败时的降级 Mock。
- `pages/learn/index.uvue`：动态渲染从 `services` 拉取的学习路径，点击路径可跳转至详情页或游戏页。
- `pages/knowledge/detail.uvue`：知识节点详情展示页面，动态接收路由参数渲染内容。

## 待完善清单 (Todo)
- [ ] 接入兼容 uni-app x 的图表库（如 ECharts），将顶部的占位文字替换为真实可交互的力导向关系图 (Force-Directed Graph)。
- [ ] 跨文化对照系统的双栏 UI 视图设计（目前仅为文本描述展示，未来可实现左中右对比视图）。
- [ ] 对接真实图谱后端的 GraphQL / RESTful 接口。
