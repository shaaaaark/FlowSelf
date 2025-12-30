# FlowSelf 设计决策文档

**Version**: 1.0
**Date**: 2025-12-30
**Author**: Claude (Frontend Architect)

---

## 1. 美学方向决策

### 选择：禅意数字花园 (Zen Digital Garden)

**决策理由**：
- FlowSelf 核心价值是"极低输入摩擦"，需要让用户感到平静、无压力
- "心流"(Flow) 概念与禅意美学高度契合
- 个人成长场景需要专注而非分心的界面

**美学特征**：
- 有机/自然 + 精致/简约的结合
- 流动感的动效（模拟呼吸节奏）
- 大量留白，减少认知负担
- 柔和的色彩过渡

**拒绝的方向**：
- ❌ 游戏化/花哨风格（分散注意力）
- ❌ 极简到冰冷的风格（缺乏温度）
- ❌ 通用的 Material/iOS 风格（无差异化）

---

## 2. 动态主题系统

### 6种情绪主题色

| 情绪 | 英文 | 主色 | 辅色 | 背景渐变 |
|------|------|------|------|----------|
| 平静 | Calm | `#2D5A5A` | `#4A7C7C` | 深青→浅青 |
| 愉悦 | Joy | `#E8A87C` | `#F5C9A8` | 暖杏→奶油 |
| 专注 | Focus | `#4A5568` | `#718096` | 靛蓝→灰蓝 |
| 活力 | Energy | `#FF6B6B` | `#FFA8A8` | 珊瑚→粉橙 |
| 沉思 | Reflect | `#9F7AEA` | `#C4A8FF` | 薰衣草→淡紫 |
| 疲惫 | Tired | `#718096` | `#A0AEC0` | 烟灰→浅灰 |

**决策理由**：
- 基于色彩心理学的情绪关联
- 避免高饱和度造成视觉疲劳
- 每种主题都有完整的渐变系统，支持平滑过渡

---

## 3. 字体选择

### 中文环境
- **标题**: Noto Serif SC (思源宋体) - 优雅、有文化底蕴
- **正文**: Noto Sans SC (思源黑体) - 清晰、现代、阅读友好

### 英文环境
- **标题**: Playfair Display - 衬线字体，高端感
- **正文**: Source Sans Pro - 无衬线，可读性强

**决策理由**：
- 衬线+无衬线组合创造层次感
- 思源字体系列完美支持中文
- 避免 Inter/Roboto 等通用字体

---

## 4. 核心交互模式

### 4.1 情绪记录（3秒原则）

```
[进入] → [选择emoji] → [可选标签] → [自动保存退出]
         ↓
    单次点击完成
```

**决策**：
- 首屏直接展示 7 个核心情绪 emoji，无需额外导航
- 标签为可选，不阻断主流程
- 自动保存，无确认按钮

### 4.2 复习卡片（手势优先）

```
← 左滑: 忘记 (Again)
↑ 上滑: 困难 (Hard)
→ 右滑: 一般 (Good)
↓ 下滑或点击: 简单 (Easy)
```

**决策**：
- 单手操作优化
- 手势区域覆盖全卡片
- 视觉反馈（颜色渐变提示方向）

### 4.3 统一输入框

**智能识别规则**：
- 以 `http://` 或 `https://` 开头 → 链接类型
- 以 `#` 开头 → 标签
- 以 `-` 或 `*` 开头 → 列表/笔记
- 纯文字 → 想法/灵感

---

## 5. 技术架构决策

### 5.1 状态管理

**选择**: Zustand + React Context

**理由**：
- Zustand 轻量，学习曲线低
- 支持持久化（配合 AsyncStorage）
- 主题切换用 Context 足够

### 5.2 本地存储

**选择**: expo-sqlite + Drizzle ORM

**理由**：
- expo-sqlite 是 Expo 官方推荐
- Drizzle ORM 类型安全，轻量
- 支持迁移，便于后续升级

### 5.3 动画库

**选择**: React Native Reanimated 3

**理由**：
- 性能最佳（运行在 UI 线程）
- 支持复杂手势交互
- Expo SDK 51+ 内置支持

### 5.4 图表库

**选择**: react-native-gifted-charts

**理由**：
- 纯 React Native 实现，无 WebView
- 支持多种图表类型
- 动画流畅

---

## 6. 目录结构

```
src/
├── components/          # 可复用 UI 组件
│   ├── ui/              # 基础 UI 元件 (Button, Card, Input)
│   ├── capture/         # 捕获相关组件
│   ├── review/          # 复习相关组件
│   └── dashboard/       # 仪表盘组件
├── screens/             # 页面级组件
├── navigation/          # 导航配置
├── stores/              # Zustand stores
├── services/            # 业务逻辑层
│   ├── ai/              # Gemini API 封装
│   ├── fsrs/            # FSRS 算法封装
│   └── sync/            # Supabase 同步
├── database/            # 数据库相关
│   ├── schema.ts        # Drizzle schema
│   └── migrations/      # 迁移文件
├── hooks/               # 自定义 hooks
├── utils/               # 工具函数
├── types/               # TypeScript 类型
└── theme/               # 主题配置
    ├── colors.ts        # 颜色定义
    ├── typography.ts    # 字体配置
    └── ThemeContext.tsx # 主题 Context
```

---

## 7. 待定决策

- [ ] 云端同步触发时机（实时 vs 手动 vs 定时）
- [ ] AI 问答生成的 prompt 优化策略
- [ ] 知识图谱可视化库选型

---

## 变更记录

| 日期 | 版本 | 变更内容 | 作者 |
|------|------|----------|------|
| 2025-12-30 | 1.0 | 初始设计决策 | Claude |
