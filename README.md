# Product Requirements Document: FlowSelf 核心功能

**Version**: 1.0
**Date**: 2025-12-30
**Author**: Sarah (Product Owner)
**Quality Score**: 92/100

---

## Executive Summary

FlowSelf（心流）是一个围绕"自我"构建的个人成长平台，融合生活记录（LifeOS）与知识成长（ThinkFlow），形成 **Work × Life × Growth** 的一体化自我管理系统。

**核心价值主张**：**极低输入摩擦** — 让记录变得毫不费力，数据自然积累，最终推动真正的成长。

本 PRD 定义了 FlowSelf 的 **5 大核心功能**，形成完整的成长闭环：
```
捕获 → 内化 → 复习 → 关联洞察 → 成长可视化
  ↑_____________________________________|
```

**目标用户**：个人使用为主，未来可扩展至小圈子分享。
**成功指标**：复习完成率高 + 数据洞察有效。
**开发周期**：3+ 月，追求完成度与细节打磨。

---

## Problem Statement

**Current Situation（现状痛点）**：
- 市面上的知识管理工具（Notion、Obsidian）注重存储，缺乏复习闭环
- 情绪/习惯追踪类应用（Daylio、Habitica）与学习工具割裂，无法分析关联
- 现有间隔重复工具（Anki）输入摩擦高，难以坚持
- 没有产品将"生活状态"与"学习效率"做深度关联分析

**Proposed Solution（解决方案）**：
FlowSelf 通过五大核心功能，打造"生活 ↔ 知识"双层融合的成长闭环：
1. **统一捕获** — 极低摩擦的一站式输入入口
2. **智能内化** — AI 自动生成可复习的知识卡片
3. **每日复习** — FSRS 算法驱动的间隔重复 + 生活回顾
4. **生活-知识关联** — 情绪与复习效率的模式识别（核心差异化）
5. **成长仪表盘** — 学习曲线 + 情绪趋势的可视化

**Business Impact（预期价值）**：
- 复习效率提升 20-30%（FSRS 算法优势）
- 通过模式识别发现"情绪-学习"关联，优化学习时机
- 极低输入摩擦带来更高的数据积累率和使用粘性

---

## Success Metrics

**Primary KPIs:**

| 指标 | 目标值 | 测量方法 |
|------|--------|----------|
| 每日复习完成率 | ≥ 80% | 已复习卡片数 / 当日待复习卡片数 |
| 周活跃使用天数 | ≥ 5 天 | 用户每周打开 App 的天数 |
| 输入到卡片转化率 | ≥ 60% | 生成的知识卡片数 / 输入的笔记数 |
| 洞察有效性评分 | ≥ 4/5 | 用户对 AI 洞察的主观评分 |

**Secondary KPIs:**
- 单次输入耗时：< 30 秒
- 每日复习耗时：5-15 分钟
- 知识保留率：30 天后 ≥ 70%

**Validation**：通过 App 内埋点 + 定期用户反馈问卷验证。

---

## User Personas

### Primary: 自我成长者

- **Role**: 追求持续成长的知识工作者/学生
- **Goals**:
  - 将零散的学习和生活记录转化为持久的成长
  - 发现自己的状态模式，优化学习效率
  - 形成稳定的复习习惯，不再遗忘学过的知识
- **Pain Points**:
  - 输入摩擦高，难以坚持记录
  - 学了就忘，缺乏有效的复习机制
  - 不清楚自己的状态如何影响学习效率
- **Technical Level**: 中级，熟悉常见效率工具

### Secondary: 小圈子成员

- **Role**: 与主用户关系亲密的朋友/家人
- **Goals**: 了解主用户的状态、学习进展
- **Pain Points**: 缺乏非侵入式了解对方状态的方式
- **Technical Level**: 初级，需要简单直观的界面

---

## User Stories & Acceptance Criteria

### Feature 1: 统一捕获（Unified Capture）

#### Story 1.1: 快速情绪记录

**As a** 自我成长者
**I want to** 在 3 秒内记录当前情绪状态
**So that** 我能积累足够的情绪数据用于后续分析

**Acceptance Criteria:**
- [ ] 首屏显示情绪 emoji 选择器（5-7 个核心情绪）
- [ ] 支持添加可选的简短标签（如：工作、学习、社交）
- [ ] 支持可选的一句话备注
- [ ] 自动记录时间戳和地理位置（可选）
- [ ] 整个流程 ≤ 3 秒完成

#### Story 1.2: 快速文字输入

**As a** 自我成长者
**I want to** 随时记录闪现的想法、链接、笔记
**So that** 我不会错过任何可能有价值的内容

**Acceptance Criteria:**
- [ ] 统一输入框支持纯文字、Markdown 语法、URL 链接
- [ ] 自动识别输入类型（想法、链接、笔记）并打标签
- [ ] 支持快捷键/手势快速唤起输入框
- [ ] 输入后自动保存，无需手动确认
- [ ] 支持离线输入，联网后自动同步

#### Story 1.3: 内容分类整理

**As a** 自我成长者
**I want to** 对捕获的内容进行简单分类
**So that** 后续能按主题查找和复习

**Acceptance Criteria:**
- [ ] 支持创建自定义标签/主题
- [ ] AI 自动建议标签（基于内容语义）
- [ ] 支持批量整理收集箱内容
- [ ] 提供"待整理"队列提醒

---

### Feature 2: 智能内化（Smart Internalization）

#### Story 2.1: AI 自动生成问答卡片

**As a** 自我成长者
**I want to** 从我的笔记自动生成问答卡片
**So that** 我不需要手动创建复习内容

**Acceptance Criteria:**
- [ ] 笔记保存后，AI 自动分析并生成候选问答对
- [ ] 用户可一键接受或编辑 AI 生成的卡片
- [ ] 支持批量审核模式
- [ ] 生成的卡片自动关联源笔记
- [ ] 支持手动创建卡片作为补充

#### Story 2.2: 知识结构化

**As a** 自我成长者
**I want to** AI 帮我整理凌乱的笔记
**So that** 知识更有条理，便于理解和复习

**Acceptance Criteria:**
- [ ] AI 自动识别笔记中的关键概念
- [ ] 建议笔记的结构化大纲
- [ ] 自动建立笔记间的双向链接
- [ ] 显示概念关系图（知识图谱）

#### Story 2.3: 卡片类型支持

**As a** 自我成长者
**I want to** 不同类型的内容有不同的复习形式
**So that** 复习更有效、更有趣

**Acceptance Criteria:**
- [ ] 支持问答卡片（正反面形式）
- [ ] 支持知识点回顾卡片（概念 + 熟悉度评估）
- [ ] 支持生活回顾卡片（过去某天的情绪/事件回顾）
- [ ] 不同卡片类型有不同的 UI 展示

---

### Feature 3: 每日复习（Daily Review）

#### Story 3.1: FSRS 智能复习队列

**As a** 自我成长者
**I want to** 系统根据遗忘曲线自动安排复习
**So that** 我用最少的时间保持最好的记忆效果

**Acceptance Criteria:**
- [ ] 实现 FSRS 算法（Free Spaced Repetition Scheduler）
- [ ] 每日自动生成复习队列
- [ ] 显示当日待复习数量和预计时间
- [ ] 支持"今日已复习"状态追踪
- [ ] 复习完成后更新卡片的下次复习时间

#### Story 3.2: 动态复习量调整

**As a** 自我成长者
**I want to** 复习量根据我的可用时间和状态自动调整
**So that** 我不会因为压力太大而放弃

**Acceptance Criteria:**
- [ ] 用户可设置每日复习时间偏好（5/10/15/20 分钟）
- [ ] 系统根据当前情绪状态调整复习强度
- [ ] 支持"快速复习"模式（仅最重要的卡片）
- [ ] 提供"今日跳过"选项（计入统计但不惩罚）

#### Story 3.3: 复习反馈收集

**As a** 自我成长者
**I want to** 复习时快速反馈我的掌握程度
**So that** 系统能优化后续的复习安排

**Acceptance Criteria:**
- [ ] 提供简单的反馈选项（忘记 / 困难 / 一般 / 简单）
- [ ] 支持手势快速反馈
- [ ] 反馈数据用于优化 FSRS 参数
- [ ] 显示当前卡片的历史复习记录

---

### Feature 4: 生活-知识关联（Life-Knowledge Bridge）

#### Story 4.1: 情绪-复习效率关联分析

**As a** 自我成长者
**I want to** 了解我的情绪状态如何影响复习效率
**So that** 我能选择最佳时机进行学习

**Acceptance Criteria:**
- [ ] 自动关联情绪记录与当日复习数据
- [ ] 分析不同情绪下的复习完成率和正确率
- [ ] 生成"最佳学习状态"洞察
- [ ] 在低效状态时给出调整建议

#### Story 4.2: 知识-知识关联分析

**As a** 自我成长者
**I want to** 发现我的笔记和知识之间的隐藏联系
**So that** 我能形成更完整的知识网络

**Acceptance Criteria:**
- [ ] AI 自动识别笔记间的语义关联
- [ ] 显示知识图谱可视化
- [ ] 推荐相关笔记和卡片
- [ ] 高亮孤立的知识点

#### Story 4.3: 模式识别与洞察

**As a** 自我成长者
**I want to** 系统自动发现我的行为模式
**So that** 我能更了解自己

**Acceptance Criteria:**
- [ ] 自动识别周期性模式（如周末效率下降）
- [ ] 发现异常模式并提醒
- [ ] 生成周度/月度洞察报告
- [ ] 洞察以简洁易懂的方式呈现

---

### Feature 5: 成长仪表盘（Growth Dashboard）

#### Story 5.1: 学习曲线可视化

**As a** 自我成长者
**I want to** 看到我的学习进度和成长轨迹
**So that** 我能保持动力并庆祝进步

**Acceptance Criteria:**
- [ ] 显示知识卡片累计数量趋势
- [ ] 显示复习完成率趋势
- [ ] 显示记忆保留率估算
- [ ] 支持按日/周/月/年查看

#### Story 5.2: 情绪趋势可视化

**As a** 自我成长者
**I want to** 看到我的情绪变化趋势
**So that** 我能更了解自己的状态模式

**Acceptance Criteria:**
- [ ] 显示情绪分布热力图
- [ ] 显示情绪趋势线
- [ ] 高亮情绪波动点
- [ ] 关联重要事件/标签

#### Story 5.3: 动态主题

**As a** 自我成长者
**I want to** App 主题根据我的情绪状态自动变化
**So that** 界面能反映我当前的状态

**Acceptance Criteria:**
- [ ] 定义 4-6 种情绪主题色
- [ ] 根据最近的情绪记录自动切换主题
- [ ] 平滑过渡动画
- [ ] 支持手动覆盖主题选择

---

## Functional Requirements

### Core Features Summary

| Feature | 核心功能 | 输入 | 输出 |
|---------|----------|------|------|
| 统一捕获 | 情绪 + 文字输入 | emoji、标签、文字、链接 | 分类存储的记录 |
| 智能内化 | AI 问答生成 + 结构化 | 笔记内容 | 知识卡片 + 概念关系 |
| 每日复习 | FSRS 队列 + 动态调整 | 用户反馈 | 更新的复习计划 |
| 生活-知识关联 | 模式识别 + 洞察 | 情绪 + 复习数据 | 关联分析报告 |
| 成长仪表盘 | 可视化 + 动态主题 | 所有数据 | 图表 + 洞察 |

### Out of Scope (MVP 不包含)

- 语音输入和转写
- 图片/截图 OCR 捕获
- 多种题型（填空、选择、判断）
- 习惯追踪与关联分析
- 多用户协作编辑
- Web 端应用（MVP 仅移动端）
- 桌面端应用（Tauri）

---

## Technical Constraints

### Performance

| 指标 | 目标 |
|------|------|
| 应用启动时间 | < 2 秒 |
| 输入响应延迟 | < 100ms |
| AI 卡片生成 | < 5 秒 |
| 离线功能 | 100% 核心功能可用 |

### Security

- **本地存储**：所有数据本地优先存储（SQLite / Realm）
- **安全同步**：仅安全状态下同步数据，使用端到端加密
- **数据导出**：支持随时导出所有数据（JSON/Markdown 格式）
- **无强制登录**：MVP 阶段可完全离线使用

### Technology Stack

**移动端**：
- React Native（跨平台移动应用）
- Expo（开发工具链）
- TypeScript

**数据层**：
- 本地：SQLite / Realm（离线优先）
- 云端：Supabase（可选同步）

**AI 集成**：
- Google Gemini API（问答生成、洞察分析）
- 本地 embedding 模型（语义关联）

**算法**：
- FSRS（Free Spaced Repetition Scheduler）

### Integration Requirements

| 系统 | 用途 | 优先级 |
|------|------|--------|
| Supabase | 用户认证 + 数据同步 | P1 |
| Google Gemini | AI 功能 | P1 |
| FSRS Library | 间隔重复算法 | P1 |
| 系统分享 | 小圈子分享 | P2 |

---

## MVP Scope & Phasing

### Phase 1: MVP (3+ 月)

**统一捕获**
- [x] 情绪 emoji 快速记录
- [x] 文字/想法输入
- [x] 自动标签建议

**智能内化**
- [x] AI 生成问答卡片
- [x] 用户编辑审核
- [x] 基础双向链接

**每日复习**
- [x] FSRS 算法实现
- [x] 动态复习量调整
- [x] 三种卡片类型支持

**生活-知识关联**
- [x] 情绪-复习关联基础分析
- [x] 基础知识图谱

**成长仪表盘**
- [x] 学习曲线图表
- [x] 情绪趋势图表
- [x] 动态主题切换

**基础设施**
- [x] React Native 移动端
- [x] 本地优先存储
- [x] 数据导出功能

### Phase 2: 完善体验

- [ ] 优化 AI 生成质量
- [ ] 添加更多洞察维度
- [ ] 小圈子分享功能
- [ ] 云端同步（Supabase）
- [ ] 推送通知提醒

### Phase 3: 扩展平台

- [ ] Web 端应用
- [ ] 桌面端应用（Tauri）
- [ ] 高级知识图谱
- [ ] 年度成长报告

### Future Considerations

- 语音输入支持
- 图片 OCR 捕获
- 习惯追踪关联
- 社区功能
- 多语言支持

---

## Risk Assessment

| Risk | Probability | Impact | Mitigation Strategy |
|------|------------|--------|---------------------|
| AI API 成本过高 | Medium | High | 本地缓存 + 请求优化 + 设置使用上限 |
| FSRS 实现复杂 | Medium | Medium | 使用开源 ts-fsrs 库 |
| React Native 性能 | Low | Medium | 性能监控 + 必要时原生优化 |
| 用户坚持使用 | Medium | High | 极低输入摩擦 + 游戏化激励 |
| 数据迁移复杂 | Low | Low | 标准数据格式 + 导入工具 |

---

## Dependencies & Blockers

**Dependencies:**

| 依赖 | 描述 | Owner |
|------|------|-------|
| React Native 环境 | 开发环境搭建 | 开发者 |
| Gemini API Key | AI 功能接入 | 开发者 |
| FSRS 算法库 | ts-fsrs 或自实现 | 开发者 |
| 设计系统 | 动态主题设计稿 | 设计师/开发者 |

**Known Blockers:**
- 无严重 blocker，可立即启动开发

---

## Appendix

### Glossary

| 术语 | 定义 |
|------|------|
| FSRS | Free Spaced Repetition Scheduler，一种基于记忆模型的间隔重复算法 |
| 知识卡片 | 可用于复习的问答对或知识点 |
| 双向链接 | 笔记之间的相互引用关系 |
| 动态主题 | 根据用户状态自动变化的 UI 主题 |
| 本地优先 | 数据优先存储在本地设备，云端同步为可选 |

### References

- [FSRS 算法论文](https://github.com/open-spaced-repetition/fsrs4anki)
- [ts-fsrs 库](https://github.com/open-spaced-repetition/ts-fsrs)
- [React Native 官方文档](https://reactnative.dev/)
- [Supabase 文档](https://supabase.com/docs)
- [Google Gemini API](https://ai.google.dev/)

### Design References

- 动态主题色彩参考：基于情绪心理学的颜色关联
- 交互设计参考：极简主义、减少认知负担

---

*This PRD was created through interactive requirements gathering with quality scoring (92/100) to ensure comprehensive coverage of business, functional, UX, and technical dimensions.*
