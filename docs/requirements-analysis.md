# 心流产品需求分析与敏捷开发 Roadmap

## 一、核心价值分析

### 1.1 用户痛点
- 生活记录分散，无法形成完整视图
- 知识输入后缺乏消化机制，遗忘率高
- 缺少结构化的自我反思工具
- 跨设备使用时数据不同步或隐私泄露

### 1.2 产品核心价值主张
**让个人成长可记录、可理解、可持续**

### 1.3 最小可验证价值（MVP 核心）
用户能够：
1. 快速记录日常碎片信息
2. 将记录内容转化为可回顾的知识
3. 通过简单的反思机制获得反馈

---

## 二、史诗（Epics）划分

### Epic 1: 基础记录能力
**目标**: 用户能够在单一设备上记录生活碎片

### Epic 2: 知识理解能力
**目标**: 用户能够将输入内容转化为可吸收的知识

### Epic 3: 反思与回顾能力
**目标**: 用户能够通过结构化方式回顾自己的记录

### Epic 4: 多端同步能力
**目标**: 用户能够在多设备间无缝切换

### Epic 5: 智能化增强
**目标**: 通过 AI 能力提升记录、理解、反思效率

### Epic 6: 隐私与工作模式
**目标**: 用户能够在工作场景安全使用

---

## 三、版本 Roadmap

### v0.1 - MVP 验证版（2-3 周）
**目标**: 验证核心价值假设

**范围**:
- 单端 Web 应用
- 基础记录功能（文本 Inbox）
- 简单的笔记管理
- 每日一问反思机制

**不包含**:
- 多端同步
- 复杂的知识处理
- 媒体管理
- 习惯追踪

---

### v0.2 - 知识增强版（3-4 周）
**目标**: 验证知识理解机制

**范围**:
- 笔记向量化
- 基于内容的问答生成
- 简单的复习机制
- 知识关联展示

---

### v0.3 - 生活记录版（3-4 周）
**目标**: 完善生活记录能力

**范围**:
- 情绪记录
- 习惯追踪
- 媒体收集
- 数据可视化（趋势图表）

---

### v0.4 - 多端同步版（4-5 周）
**目标**: 实现跨设备体验

**范围**:
- 移动端应用
- 桌面端应用
- 实时同步
- 离线支持

---

### v0.5 - 智能化版（4-6 周）
**目标**: AI 能力深度集成

**范围**:
- 工作流引擎集成
- 自动内容解析
- 智能问答生成
- 自适应复习算法

---

### v1.0 - 完整体验版（3-4 周）
**目标**: 产品完整性与稳定性

**范围**:
- 工作/私人模式切换
- 完整的隐私控制
- 性能优化
- 用户引导体系

---

## 四、v0.1 MVP 用户故事拆解

### Story 1.1: 快速记录想法
**作为** 用户
**我想要** 快速记录脑海中的想法
**以便于** 不丢失灵感和待办事项

**验收标准**:
- [ ] 打开应用后 3 秒内可以开始输入
- [ ] 支持 Markdown 格式
- [ ] 自动保存，无需手动操作
- [ ] 记录带时间戳

**技术任务**:
- 搭建 Next.js 项目基础
- 实现文本编辑器组件
- 集成 Supabase 认证
- 实现数据自动保存

---

### Story 1.2: 查看历史记录
**作为** 用户
**我想要** 按时间查看我的历史记录
**以便于** 回顾过去的想法

**验收标准**:
- [ ] 按日期倒序展示记录
- [ ] 支持按日期筛选
- [ ] 显示记录创建时间
- [ ] 支持搜索功能

**技术任务**:
- 实现列表组件
- 实现日期筛选器
- 实现全文搜索
- 优化列表性能

---

### Story 1.3: 整理为笔记
**作为** 用户
**我想要** 将碎片记录整理成结构化笔记
**以便于** 形成完整的知识内容

**验收标准**:
- [ ] 可以创建笔记文档
- [ ] 支持富文本编辑
- [ ] 可以从 Inbox 拖拽内容到笔记
- [ ] 笔记支持标签分类

**技术任务**:
- 实现笔记编辑器
- 实现拖拽功能
- 实现标签系统
- 设计笔记数据模型

---

### Story 1.4: 每日反思
**作为** 用户
**我想要** 每天回答一个反思问题
**以便于** 保持自我觉察

**验收标准**:
- [ ] 每天推送一个反思问题
- [ ] 可以记录答案
- [ ] 可以查看历史回答
- [ ] 问题库可配置

**技术任务**:
- 设计问题数据模型
- 实现问题推送逻辑
- 实现答案记录功能
- 实现历史查看页面

---

### Story 1.5: 用户认证
**作为** 用户
**我想要** 安全地登录和保存数据
**以便于** 保护我的隐私

**验收标准**:
- [ ] 支持邮箱密码登录
- [ ] 支持第三方登录（Google）
- [ ] 数据与账号绑定
- [ ] 支持登出

**技术任务**:
- 集成 Supabase Auth
- 实现登录页面
- 实现路由守卫
- 配置 RLS 策略

---

## 五、v0.1 架构设计

### 5.1 技术栈
```
前端: Next.js 14 (App Router) + TypeScript + Tailwind CSS
状态管理: Zustand
数据库: Supabase (PostgreSQL)
认证: Supabase Auth
部署: Vercel
```

### 5.2 数据模型

```sql
-- 用户表（Supabase Auth 自带）

-- Inbox 记录表
CREATE TABLE inbox_items (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  content TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  archived BOOLEAN DEFAULT FALSE
);

-- 笔记表
CREATE TABLE notes (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  title TEXT NOT NULL,
  content TEXT,
  tags TEXT[],
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- 每日问题表
CREATE TABLE daily_questions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  question TEXT NOT NULL,
  category TEXT,
  active BOOLEAN DEFAULT TRUE
);

-- 每日回答表
CREATE TABLE daily_answers (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  question_id UUID REFERENCES daily_questions,
  answer TEXT NOT NULL,
  answered_at DATE DEFAULT CURRENT_DATE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 5.3 项目结构

```
flowself/
├── apps/
│   └── web/                    # Next.js Web 应用
│       ├── app/
│       │   ├── (auth)/
│       │   │   ├── login/
│       │   │   └── signup/
│       │   ├── (app)/
│       │   │   ├── inbox/
│       │   │   ├── notes/
│       │   │   └── reflect/
│       │   └── layout.tsx
│       ├── components/
│       ├── lib/
│       └── package.json
├── packages/
│   ├── types/                  # 共享类型定义
│   ├── ui/                     # 共享 UI 组件
│   └── database/               # 数据库类型与工具
├── turbo.json
└── package.json
```

### 5.4 核心流程

**记录流程**:
```
用户输入 → 自动保存到 Supabase → 实时更新列表
```

**整理流程**:
```
选择 Inbox 项 → 拖拽到笔记 → 更新笔记内容 → 归档 Inbox 项
```

**反思流程**:
```
每日首次访问 → 检查是否已回答 → 展示问题 → 记录答案 → 标记完成
```

---

## 六、v0.2 知识增强版架构分析

### 6.1 新增能力
- 向量化存储
- 语义检索
- 自动问答生成
- 间隔重复算法

### 6.2 技术栈扩展
```
向量数据库: Supabase pgvector
AI 服务: OpenAI API / Claude API
工作流: n8n (自托管或云端)
```

### 6.3 数据模型扩展

```sql
-- 启用向量扩展
CREATE EXTENSION IF NOT EXISTS vector;

-- 笔记向量表
CREATE TABLE note_embeddings (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  note_id UUID REFERENCES notes ON DELETE CASCADE,
  content_chunk TEXT,
  embedding VECTOR(1536),
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 问答卡片表
CREATE TABLE qa_cards (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  note_id UUID REFERENCES notes,
  question TEXT NOT NULL,
  answer TEXT NOT NULL,
  difficulty INTEGER DEFAULT 0,
  next_review_at TIMESTAMPTZ,
  review_count INTEGER DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 复习记录表
CREATE TABLE review_logs (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  card_id UUID REFERENCES qa_cards,
  quality INTEGER, -- 0-5 评分
  reviewed_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 6.4 核心流程

**向量化流程**:
```
笔记保存 → Webhook 触发 n8n → 内容分块 → 调用 OpenAI Embedding → 存储向量
```

**问答生成流程**:
```
笔记完成 → 提取关键内容 → 调用 LLM 生成问答 → 存储为卡片 → 加入复习队列
```

**复习流程**:
```
每日登录 → 查询到期卡片 → 展示问题 → 用户作答 → 评分 → 更新下次复习时间
```

---

## 七、v0.3 生活记录版架构分析

### 7.1 新增能力
- 情绪追踪
- 习惯打卡
- 媒体管理
- 数据可视化

### 7.2 技术栈扩展
```
文件存储: Cloudflare R2
图表库: Recharts / Chart.js
图片处理: Sharp
```

### 7.3 数据模型扩展

```sql
-- 情绪记录表
CREATE TABLE mood_logs (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  mood_score INTEGER CHECK (mood_score BETWEEN 1 AND 10),
  note TEXT,
  logged_at TIMESTAMPTZ DEFAULT NOW()
);

-- 习惯表
CREATE TABLE habits (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  name TEXT NOT NULL,
  frequency TEXT, -- daily, weekly
  active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 习惯打卡表
CREATE TABLE habit_logs (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  habit_id UUID REFERENCES habits,
  completed BOOLEAN DEFAULT TRUE,
  logged_at DATE DEFAULT CURRENT_DATE,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 媒体表
CREATE TABLE media_items (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  type TEXT, -- image, video, audio, document
  url TEXT NOT NULL,
  thumbnail_url TEXT,
  title TEXT,
  tags TEXT[],
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

---

## 八、v0.4 多端同步版架构分析

### 8.1 新增能力
- 移动端应用
- 桌面端应用
- 实时同步
- 离线支持

### 8.2 技术栈扩展
```
移动端: React Native / Expo
桌面端: Tauri
本地存储: SQLite
同步: Supabase Realtime
```

### 8.3 项目结构扩展

```
flowself/
├── apps/
│   ├── web/
│   ├── mobile/                 # React Native
│   └── desktop/                # Tauri
├── packages/
│   ├── types/
│   ├── ui/
│   ├── database/
│   └── sync/                   # 同步逻辑
```

### 8.4 同步策略

**在线模式**:
```
操作 → 本地立即更新 → 后台同步到 Supabase → 其他设备实时接收
```

**离线模式**:
```
操作 → 本地 SQLite → 标记为待同步 → 网络恢复后批量上传 → 冲突解决
```

---

## 九、开发优先级建议

### 第一优先级（v0.1）
1. 用户认证
2. Inbox 记录
3. 笔记管理
4. 每日反思

### 第二优先级（v0.2）
1. 向量化存储
2. 问答生成
3. 复习机制

### 第三优先级（v0.3）
1. 情绪追踪
2. 习惯管理
3. 数据可视化

### 第四优先级（v0.4）
1. 移动端
2. 桌面端
3. 离线支持

---

## 十、风险与依赖

### 技术风险
- 向量检索性能（大量数据时）
- 多端同步冲突处理
- AI 调用成本控制

### 外部依赖
- Supabase 服务稳定性
- OpenAI API 可用性
- Cloudflare 服务限制

### 缓解措施
- 提前进行性能测试
- 设计降级方案
- 控制 AI 调用频率
- 准备备用服务商

---

## 十一、成功指标

### v0.1 验证指标
- 用户能在 5 分钟内完成首次记录
- 每日活跃用户留存率 > 40%
- 平均每用户每天记录 > 3 条

### v0.2 验证指标
- 用户完成首次复习率 > 60%
- 问答卡片质量满意度 > 70%

### v0.3 验证指标
- 习惯打卡连续天数中位数 > 7 天
- 数据可视化页面访问率 > 50%

### v0.4 验证指标
- 多端使用用户占比 > 30%
- 同步成功率 > 99%

---

## 十二、下一步行动

1. 确认 v0.1 范围与优先级
2. 搭建 Monorepo 基础架构
3. 配置 Supabase 项目
4. 开始 Story 1.1 开发
