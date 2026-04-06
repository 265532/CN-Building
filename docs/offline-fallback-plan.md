# 全面降级无网络离线处理实施计划

## 1. 概述 (Overview)
为了提升“中国古建筑智慧学习平台”在弱网或无网络环境下的用户体验，本项目将实施全面的**无感降级 (Seamless Degradation)** 处理。
在请求后端接口失败时，系统将自动切换至本地预置的模拟数据进行渲染，确保用户能够持续浏览和学习，且在视觉上不体现任何“降级”或“模拟”字样，保持生产环境真实感。

## 2. 基础设施调整 (Infrastructure Changes)

### 2.1 增强 `utils/request.uts`
修改通用的请求工具，使其具备自动降级能力。

- **核心逻辑**：在 `uni.request` 的 `fail` 回调或 `statusCode !== 200` 时，根据请求的 URL 自动分发到对应的 Mock 数据处理器。
- **配置项**：在 `RequestOptions` 中增加 `useMockOnFailure: boolean` (默认为 `true`)。

```typescript
// 伪代码参考
export function request<T>(options: RequestOptions): Promise<T> {
  return new Promise((resolve, reject) => {
    uni.request({
      // ... 现有逻辑
      success: (res) => {
        if (res.statusCode >= 200 && res.statusCode < 300) {
          resolve(res.data as T)
        } else if (options.useMockOnFailure !== false) {
          // 触发降级
          resolve(getMockDataByUrl(options.url) as T)
        } else {
          reject(new Error(`Server error: ${res.statusCode}`))
        }
      },
      fail: () => {
        if (options.useMockOnFailure !== false) {
          // 触发网络故障降级
          resolve(getMockDataByUrl(options.url) as T)
        } else {
          reject(new Error('Network failure'))
        }
      }
    })
  })
}
```

## 3. Mock 数据仓库管理 (Mock Data Repository)

### 3.1 目录结构
创建 `services/mock/` 目录，按业务模块划分数据文件：
- `services/mock/home.uts`
- `services/mock/knowledge.uts`
- `services/mock/user.uts`
- `services/mock/achievement.uts`
- `services/mock/ai.uts`
- `services/mock/learn.uts`
- `services/mock/map.uts`

### 3.2 各模块 Mock 数据规范
**严格要求**：所有文本描述、图片路径、数值状态必须贴合真实古建筑业务场景，严禁出现 "Mock"、"测试"、"降级" 等词汇。

#### A. 首页 (Home)
- **Banner**: 故宫全景导览、应县木塔结构解析、古建营造技法课程。
- **专题**: 皇宫专题、民居专题、官府专题、园林专题。
- **跨文化对照**: 斗拱与希腊柱式、飞檐与哥特尖顶。

#### B. 知识百科 (Knowledge)
- **节点数据**: 提供至少 10 个核心节点的详细内容（如：斗拱、梁架、瓦作、彩画、须弥座等）。
- **关联关系**: 确保节点间的 `relatedNodes` 逻辑闭环。

#### C. AI 助手 (AI)
- **预设对话**: 提供常见问题的标准 AI 回复（如：“什么是五脊六兽？”）。
- **拍照识别结果**: 预置几组识别结果（如：北京故宫太和殿、山西应县木塔）。

#### D. 地图打卡 (Map)
- **标记点**: 预置全国主要古建筑景点的经纬度、名称、地址及美图。
- **周边推荐**: 模拟根据坐标返回的最近古建筑列表。

#### E. 个人中心 (Profile/User)
- **用户信息**: 预置一个中级学习者账号，拥有一定数量的勋章和学习记录。
- **雷达图数据**: 均衡且合理的古建筑知识分布（木作、砖瓦、装饰、文化、布局）。

#### F. 学习路径 (Learn)
- **课程列表**: 包含“入门：认识五脊六兽”、“进阶：营造法式初探”等路径。

## 4. 实施步骤 (Implementation Steps)

1. **第一阶段：Mock 数据编写**
   - 按照 `types/` 下的定义，在 `services/mock/` 中实现各模块的 Mock 返回函数。
2. **第二阶段：通用请求拦截**
   - 在 `utils/request.uts` 中集成 `getMockDataByUrl` 分发逻辑。
3. **第三阶段：Service 层清理**
   - 移除 `services/knowledge.uts`、`services/user.uts` 等文件中分散的 `getMock...` 硬编码逻辑，全部收纳至 `services/mock/`。
   - 确保所有 Service 函数均调用 `utils/request.uts`。
4. **第四阶段：视觉验证**
   - 检查所有 UI 组件，确保无网络状态下页面依然饱满且无任何“异常提示”干扰。

## 5. 验收标准 (Acceptance Criteria)
- [ ] 断开网络后，首页能正常加载 Banner 和专题。
- [ ] 点击知识点能进入详情页，内容完整无缺。
- [ ] 个人中心显示完整的学习记录和雷达图。
- [ ] 地图页面显示预置的古建筑标记点。
- [ ] AI 对话功能在无网状态下仍能回复预置内容。
- [ ] **全站严禁出现“[降级处理]”等标记。**
