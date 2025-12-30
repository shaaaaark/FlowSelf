# 情绪记录模块 - 开发计划

## 概述
构建基于 Expo 52 + React Native 的情绪记录功能，支持 7 种情绪快速捕获、动态主题切换、本地持久化，遵循 3 秒原则实现极低输入摩擦。

## 任务分解

### Task 1: 路由脚手架 + 主题系统
- **ID**: task-1
- **type**: ui
- **描述**: 构建 expo-router 文件路由结构、实现 6 种情绪主题系统（基于 React Context）、配置渐变背景动画
- **文件范围**:
  - `app/_layout.tsx` - 根布局 + ThemeProvider 集成
  - `app/index.tsx` - 首页重定向逻辑
  - `app/(capture)/index.tsx` - 情绪捕获页面路由
  - `src/theme/colors.ts` - 6 种情绪主题色定义
  - `src/theme/ThemeContext.tsx` - 主题 Context + 切换逻辑
  - `src/theme/typography.ts` - 字体配置（思源字体）
  - `src/screens/capture/CaptureScreen.tsx` - 捕获页面容器
  - `src/components/ui/GradientBackground.tsx` - Reanimated 渐变背景
- **依赖**: None
- **测试命令**: `npm test -- src/theme/__tests__ --coverage --collectCoverageFrom='src/theme/**/*.{ts,tsx}'`
- **测试重点**:
  - ThemeContext 主题切换逻辑（6 种情绪主题）
  - 主题色值正确映射（calm → #2D5A5A, joy → #E8A87C 等）
  - 字体配置加载成功
  - GradientBackground 接收主题 props 并渲染
- **预期测试覆盖率贡献**: 15%

---

### Task 2: 数据库 + 数据访问层
- **ID**: task-2
- **type**: default
- **描述**: 使用 Drizzle ORM 定义 emotion_logs 和 settings 表，实现迁移脚本、封装 emotionLogService 和 settingsService
- **文件范围**:
  - `src/database/schema.ts` - Drizzle schema 定义（emotionLogs, settings 表）
  - `src/database/client.ts` - expo-sqlite 客户端初始化
  - `src/database/migrations/0001_initial.sql` - 初始迁移脚本
  - `src/services/emotionLogService.ts` - CRUD 操作（createLog, getLogs, getLogsByDateRange）
  - `src/services/settingsService.ts` - 配置读写（getSetting, setSetting）
  - `src/types/emotion.ts` - TypeScript 类型定义（EmotionLog, EmotionKey, ThemeKey）
- **依赖**: None
- **测试命令**: `npm test -- src/database/__tests__ src/services/__tests__ --coverage --collectCoverageFrom='src/{database,services}/**/*.{ts,tsx}'`
- **测试重点**:
  - 数据库初始化成功（表创建）
  - emotionLogService.createLog 插入数据并返回 ID
  - emotionLogService.getLogs 按时间倒序查询
  - settingsService 读写配置（theme_preference, last_mood）
  - 边界情况：空数据库查询、无效情绪键、JSON 序列化
- **预期测试覆盖率贡献**: 25%

---

### Task 3: 情绪选择器 UI
- **ID**: task-3
- **type**: ui
- **描述**: 实现 7 个情绪按钮组件、Reanimated 入场动画（stagger）、触觉反馈集成、选中态视觉效果
- **文件范围**:
  - `src/components/capture/EmotionSelector.tsx` - 7 情绪网格布局
  - `src/components/capture/EmotionButton.tsx` - 单个情绪按钮（emoji + 文字）
  - `src/components/capture/AutoSaveIndicator.tsx` - 自动保存提示组件
  - `src/hooks/useHapticFeedback.ts` - 封装 expo-haptics
  - `src/utils/emotions.ts` - 情绪元数据配置（emoji, 中英文名称映射）
- **依赖**: task-1
- **测试命令**: `npm test -- src/components/capture/__tests__ src/hooks/__tests__/useHapticFeedback.test.ts --coverage --collectCoverageFrom='src/{components/capture,hooks}/**/*.{ts,tsx}'`
- **测试重点**:
  - EmotionSelector 渲染 7 个 EmotionButton
  - EmotionButton 点击触发 onPress + 触觉反馈
  - 选中态样式变化（scale 动画、border 高亮）
  - useHapticFeedback hook 调用 Haptics.impactAsync('medium')
  - emotions.ts 包含完整 7 种情绪元数据
- **预期测试覆盖率贡献**: 20%

---

### Task 4: 状态管理 + 自动保存
- **ID**: task-4
- **type**: default
- **描述**: 创建 Zustand stores（情绪捕获状态、设置持久化）、实现自动保存逻辑（debounce 500ms）、主题偏好持久化
- **文件范围**:
  - `src/stores/useEmotionCaptureStore.ts` - 当前选择、标签、自动保存状态
  - `src/stores/useSettingsStore.ts` - 主题偏好、最后情绪（zustand persist 中间件）
  - `src/hooks/useAutoSave.ts` - 自动保存 hook（debounce + emotionLogService）
  - `src/hooks/useThemePersistence.ts` - 主题持久化 hook
  - `src/services/autoSaveService.ts` - 自动保存业务逻辑封装
- **依赖**: task-2
- **测试命令**: `npm test -- src/stores/__tests__ src/hooks/__tests__/useAutoSave.test.ts src/services/__tests__/autoSaveService.test.ts --coverage --collectCoverageFrom='src/{stores,hooks,services}/**/*.{ts,tsx}'`
- **测试重点**:
  - useEmotionCaptureStore 状态变更（setEmotion, addTag, clearSelection）
  - useSettingsStore 持久化（模拟 AsyncStorage 读写）
  - useAutoSave 在情绪变更后 500ms 触发保存
  - autoSaveService 调用 emotionLogService.createLog
  - 边界情况：连续变更防抖、空选择不保存、保存失败回退
- **预期测试覆盖率贡献**: 25%

---

### Task 5: 测试框架 + 集成测试
- **ID**: task-5
- **type**: default
- **描述**: 配置 Jest + React Native Testing Library、编写组件集成测试、端到端流程测试、确保覆盖率 ≥90%
- **文件范围**:
  - `jest.config.js` - Jest 配置（transformIgnorePatterns, preset）
  - `jest.setup.js` - 测试环境初始化（mock expo-sqlite, expo-haptics）
  - `src/__tests__/integration/emotionCaptureFlow.test.tsx` - 端到端流程测试
  - `package.json` - 添加测试脚本和依赖（@testing-library/react-native, jest-expo）
- **依赖**: task-1, task-2, task-3, task-4
- **测试命令**: `npm test -- --coverage --coverageThreshold='{"global":{"branches":90,"functions":90,"lines":90,"statements":90}}'`
- **测试重点**:
  - 集成测试：选择情绪 → 触发主题切换 → 自动保存 → 数据持久化
  - mock expo-sqlite 返回测试数据
  - mock expo-haptics 验证触觉反馈调用
  - 覆盖所有验收标准场景
  - 生成覆盖率报告并验证 ≥90%
- **预期测试覆盖率贡献**: 15%

---

## 验收标准
- [ ] 情绪选择器支持 7 种情绪（😌 平静, 😊 愉悦, 🎯 专注, ⚡ 活力, 🤔 沉思, 😴 疲惫, 😐 中性）
- [ ] 点击情绪按钮触发触觉反馈（Haptics.impactAsync）
- [ ] 主题根据选择的情绪自动切换（6 种主题色渐变过渡）
- [ ] 情绪数据持久化到 expo-sqlite（emotion_logs 表）
- [ ] 主题偏好持久化（settings 表）
- [ ] 自动保存延迟 500ms（防抖优化）
- [ ] 单元测试覆盖率 ≥90%（branches, functions, lines, statements）
- [ ] 所有测试用例通过（npm test）

---

## 技术要点

### 关键技术决策
1. **路由**: expo-router 4.0 文件路由 + 路由组 `(capture)`
2. **状态管理**: Zustand 5（情绪状态） + React Context（主题）
3. **数据库**: expo-sqlite 15 + Drizzle ORM 0.38（类型安全）
4. **动画**: React Native Reanimated 3.16（UI 线程动画）
5. **触觉反馈**: expo-haptics 14（impactAsync 中度触感）

### 约束条件
- **新架构**: `newArchEnabled: true`（React Native 0.76）
- **路径别名**: `@/*` 指向 `src/*`（tsconfig.json）
- **覆盖率阈值**: branches/functions/lines/statements ≥90%
- **自动保存延迟**: 500ms debounce（平衡性能与数据安全）

### 测试策略
- **单元测试**: 使用 @testing-library/react-native
- **mock 策略**: expo-sqlite 使用内存数据库、expo-haptics mock 函数调用
- **集成测试**: 覆盖完整情绪捕获流程（UI → 状态 → 数据库）
- **覆盖率工具**: Jest --coverage with istanbul

### 依赖关系图
```
task-1 (路由+主题) ──┐
                    ├──→ task-3 (情绪选择器 UI)
                    │                      ↓
task-2 (数据库)  ────┼──→ task-4 (状态管理) → task-5 (测试框架)
                    │                      ↑
                    └──────────────────────┘
```

### 风险与缓解
| 风险 | 缓解措施 |
|------|----------|
| expo-sqlite 新架构兼容性 | Task 2 优先验证数据库初始化 |
| Reanimated 动画性能 | Task 3 使用 worklet + UI 线程动画 |
| 测试覆盖率不足 | Task 5 要求集成测试覆盖端到端流程 |
| 主题切换闪烁 | Task 1 使用 interpolateColor 平滑过渡 |

---

**文档版本**: 1.0
**生成时间**: 2025-12-30
**适用技术栈**: Expo 52, React Native 0.76, TypeScript 5.6
**测试框架**: Jest + React Native Testing Library
