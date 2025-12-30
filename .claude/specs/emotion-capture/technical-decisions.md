# 情绪记录模块 - 技术决策

**Version**: 1.0
**Date**: 2025-12-30
**Status**: Confirmed by codeagent analysis

---

## 1. 上下文与约束

### 1.1 技术栈
- **框架**: Expo 52, React Native 0.76.5
- **路由**: expo-router 4.0 (file-based routing)
- **状态管理**: Zustand 5 + React Context
- **数据库**: expo-sqlite 15 + Drizzle ORM 0.38
- **动画**: React Native Reanimated 3.16
- **其他**: expo-haptics, expo-linear-gradient, date-fns

### 1.2 现有资产
- `package.json`, `app.json`, `tsconfig.json` 已配置
- `docs/DESIGN_DECISIONS.md` 定义了设计系统
- `src/` 目录存在但为空
- `app/` 目录不存在（expo-router 需要）

### 1.3 约束
- `tsconfig.json` 定义了 `@/*` 路径别名指向 `src/*`
- `newArchEnabled: true` 启用 React Native 新架构
- expo-sqlite 已启用 FTS (全文搜索)

---

## 2. 架构决策

### 2.1 目录结构

```
flowself/
├── app/                    # expo-router 路由 (新建)
│   ├── _layout.tsx         # 根布局 + ThemeProvider
│   ├── index.tsx           # 首页重定向
│   └── (capture)/          # 情绪捕获路由组
│       └── index.tsx       # 捕获页面入口
├── src/                    # 共享代码
│   ├── components/
│   │   ├── ui/             # 基础 UI 组件
│   │   └── capture/        # 情绪捕获组件
│   ├── screens/
│   │   └── capture/        # 捕获页面组件
│   ├── stores/             # Zustand stores
│   ├── services/           # 业务逻辑
│   ├── database/           # Drizzle schema + migrations
│   ├── theme/              # 主题配置 + Context
│   ├── hooks/              # 自定义 hooks
│   ├── types/              # TypeScript 类型
│   └── utils/              # 工具函数
└── assets/
    ├── fonts/
    └── images/
```

### 2.2 状态管理策略

| 关注点 | 方案 | 理由 |
|--------|------|------|
| 情绪捕获状态 | `useEmotionCaptureStore` (Zustand) | 当前选择、标签、自动保存状态 |
| 主题状态 | `ThemeContext` (React Context) | 主题令牌、颜色、动画值 |
| 设置持久化 | `useSettingsStore` (Zustand + persist) | 主题偏好、最后情绪 |

**决策理由**: 符合 `DESIGN_DECISIONS.md` 中 Zustand + Context 的架构选择，按领域分离关注点。

### 2.3 数据库 Schema

```typescript
// src/database/schema.ts
import { sqliteTable, text, integer } from 'drizzle-orm/sqlite-core';

export const emotionLogs = sqliteTable('emotion_logs', {
  id: integer('id').primaryKey({ autoIncrement: true }),
  emotionKey: text('emotion_key').notNull(),  // calm, joy, focus, energy, reflect, tired, neutral
  emoji: text('emoji').notNull(),              // 😌, 😊, 🎯, ⚡, 🤔, 😴, 😐
  themeKey: text('theme_key').notNull(),       // 对应的主题键
  tags: text('tags', { mode: 'json' }),        // JSON 数组
  note: text('note'),                          // 可选备注
  createdAt: integer('created_at').notNull(),  // Unix timestamp ms
});

export const settings = sqliteTable('settings', {
  key: text('key').primaryKey(),
  value: text('value').notNull(),
});
```

### 2.4 组件层级

```
CaptureScreen
├── GradientBackground (Animated)
├── EmotionSelector
│   └── EmotionButton × 7 (Reanimated + Haptics)
├── TagChips (可选)
└── AutoSaveIndicator
```

### 2.5 动画策略

| 动画 | 实现 |
|------|------|
| 情绪按钮入场 | Reanimated stagger + entering 动画 |
| 选中效果 | scale + ripple (withSpring) |
| 主题过渡 | interpolateColor + 背景渐变 crossfade |
| 触觉反馈 | expo-haptics impactAsync |

---

## 3. 任务分解

### T1: 路由脚手架 + 主题系统
- **类型**: `ui`
- **范围**: `app/`, `src/theme/`, `src/screens/`
- **依赖**: 无
- **交付物**: 基础路由结构、主题令牌、ThemeContext、渐变背景

### T2: 数据库 + 数据访问层
- **类型**: `default`
- **范围**: `src/database/`, `src/services/`
- **依赖**: 无
- **交付物**: Drizzle schema、迁移、emotionLogService、settingsService

### T3: 情绪选择器 UI
- **类型**: `ui`
- **范围**: `src/components/capture/`, `src/screens/capture/`
- **依赖**: T1
- **交付物**: EmotionSelector、EmotionButton、动画 + 触觉反馈

### T4: 状态管理 + 自动保存
- **类型**: `default`
- **范围**: `src/stores/`, `src/services/`, `src/hooks/`
- **依赖**: T2
- **交付物**: Zustand stores、自动保存逻辑、主题持久化

### T5: 测试框架 + 覆盖率
- **类型**: `default`
- **范围**: `package.json`, `jest.config.*`, `src/**/__tests__/`
- **依赖**: T1, T2, T3, T4
- **交付物**: Jest + RTL 配置、单元测试、覆盖率 ≥90%

---

## 4. UI 需求确认

**needs_ui**: `true`

**证据**:
- `docs/DESIGN_DECISIONS.md`: 情绪选择器 + 动态主题 + Reanimated 动画
- `.claude/specs/emotion-capture/requirements.md`: 7 情绪选择器、主题切换、触觉反馈

---

## 5. 后端路由

| Task | Type | Preferred Backend | Actual Backend |
|------|------|-------------------|----------------|
| T1 | ui | gemini | gemini |
| T2 | default | codex | codex |
| T3 | ui | gemini | gemini |
| T4 | default | codex | codex |
| T5 | default | codex | codex |

---

## 变更记录

| 日期 | 版本 | 变更内容 |
|------|------|----------|
| 2025-12-30 | 1.0 | 初始技术决策 |
