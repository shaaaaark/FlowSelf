# 🌊 **FlowSelf（心流）**

**Work × Life × Growth Unified OS**
**你的个人成长平台**

---

## 📘 Overview | 概述

**FlowSelf（中文名：心流）** 是一个围绕“自我”构建的个人平台。
它融合 LifeOS（生活记录）与 ThinkFlow（知识成长），形成：

> **Work × Life × Growth 的一体化自我管理与成长平台。**

FlowSelf 旨在帮助你：

- 看见自己的生活状态
- 让阅读变成理解
- 让记录变成成长
- 让学习有反馈、能反刍
- 保持持续的专注与心流状态

最终目标：

> **更理解自己 → 更专注 → 更成长。**

---

# 🌱 Philosophy | 核心理念

### **1. 记录不是目的，成长才是**

人生和知识都应形成闭环：
**输入 → 理解 → 反刍 → 成长**

### **2. 学而不思则罔**

阅读不等于学习，理解才是学习。
FlowSelf 用结构化、反思与问答促进深度理解。

### **3. 思而不学则殆**

持续输入是必要的。
FlowSelf 提供低摩擦的输入路径，让你随时捕捉知识与生活。

### **4. 心流是最理想的成长状态**

整个平台的核心目的，就是让你更容易进入稳定、专注、积极的成长通道——心流（Flow）。

---

# 🧩 System Architecture | 平台结构

FlowSelf 由 **两个世界 + 一个成长闭环** 构成。

---

## **A. Life Layer（生活层）**

管理你的真实行为、状态与生活轨迹：

- 情绪记录
- 习惯体系
- 稍后读
- 图片 / 视频 / 媒体
- Inbox 收集箱
- 健康数据（睡眠 / 步数）
- 工作模式（Work Mode：工作/私人分离）
- 社交能量管理

> **这是你的外在世界。**

---

## **B. Knowledge Layer（知识层）**

管理你的学习、技能与知识成长：

- 技术与工作笔记
- 学习资料整理
- 知识结构化
- 面试题 / 反刍问答
- 每日复习任务
- 反思与总结
- 学习曲线追踪

> **这是你的内在世界。**

---

## **C. Growth Loop（成长闭环）**

FlowSelf 的核心机制：
所有输入最终流向成长。

### 1. **记录（Input）**

来自生活与知识的输入。

### 2. **理解（Process / Insight）**

结构化、整理、回顾，形成深度理解。

### 3. **反刍（Reflection / Review）**

通过复盘、反思、问答将理解固化为自己的能力。

> **这是 FlowSelf 的灵魂。**

---

# 🏗️ Technical Architecture | 技术架构

## Monorepo 结构

```
FlowSelf/
├── apps/
│   └── web/              # Web 应用（React + Vite）
├── packages/
│   ├── types/            # 共享类型定义
│   ├── ui/               # 共享 UI 组件
│   └── utils/            # 共享工具函数（含 AI 服务）
├── docs/                 # 架构文档与需求分析
├── turbo.json            # Turbo 配置
└── pnpm-workspace.yaml   # PNPM workspace 配置
```

## 技术栈

**前端**：
- React 19 + TypeScript
- Vite（构建工具）
- Tailwind CSS（样式）
- Lucide React（图标）
- Recharts（数据可视化）

**AI 集成**：
- Google Gemini API（情绪分析与智能建议）

**未来扩展**：
- Supabase（后端 + 数据库 + 认证）
- Cloudflare Workers + R2（边缘计算 + 存储）
- React Native（移动端）
- Tauri（桌面端）
- n8n（工作流引擎）

---

# 🌐 Key Features | 核心特性（抽象层）

FlowSelf 的六大核心能力：

1. **自我回顾（Reflection）**
2. **自我追踪（Tracking）**
3. **自我监督（Quiz / Workflow）**
4. **自我认知（Insight）**
5. **自我成长（Review / Evolution）**
6. **自我保护（Privacy / Work Mode）**

这些能力构成平台的“骨骼”，具体功能只是表现。

---

# 🚀 Roadmap

### **Phase 1: Monorepo 架构搭建（当前阶段 - 70% 完成）**

- [x] 搭建 Turbo + PNPM Monorepo 基础架构
- [x] 从 LifeOs demo 迁移核心功能
- [x] 提取共享代码到 packages
- [ ] 修复组件 import 路径（进行中）
- [ ] 配置 TypeScript 路径别名
- [ ] 安装依赖并测试运行
- [ ] 验证所有功能正常运行

**详细进度**：查看 [迁移进度报告](./docs/migration-progress.md)

**包含功能**：
- Dashboard（仪表盘）
- Mood Tracker（情绪追踪）
- Inbox（收集箱）
- Notes（笔记管理）
- Knowledge Base（知识库 + 闪卡复习）
- Subscriptions（订阅管理）
- Social CRM（人脉能量管理）
- Flow Center（稍后读）
- Work Mode（工作模式）
- AI Insights（Gemini 集成）

### **Phase 2: 统一后台集成**

- [ ] Supabase 后端搭建
- [ ] 用户认证系统
- [ ] 数据持久化
- [ ] 实时同步机制
- [ ] 离线支持（桌面端）

### **Phase 3: 多端扩展**

- [ ] 移动端应用（React Native）
- [ ] 桌面端应用（Tauri）
- [ ] 跨端数据同步
- [ ] 统一 SDK 封装

### **Phase 4: 知识增强**

- [ ] 笔记向量化
- [ ] 语义检索
- [ ] AI 问答生成
- [ ] 间隔重复算法优化
- [ ] 知识图谱可视化

### **Phase 5: 智能化与自动化**

- [ ] 工作流引擎集成（n8n）
- [ ] 自动内容解析
- [ ] 智能推荐
- [ ] 自适应学习路径

### **v1.0 – FlowSelf Release**

- [ ] 完整的成长闭环
- [ ] 年度成长报告
- [ ] 性能优化与稳定性
- [ ] 用户引导体系

---

# 🚀 Quick Start | 快速开始

## 环境要求

- Node.js >= 18
- PNPM >= 9.15.0

## 安装与运行

```bash
# 安装 PNPM（如果还没有）
npm install -g pnpm

# 安装依赖
pnpm install

# 启动开发服务器
pnpm dev

# 构建生产版本
pnpm build
```

## 配置 AI 功能（可选）

如需使用 AI 情绪分析和智能建议功能，需要配置 Google Gemini API：

1. 在 `apps/web` 目录创建 `.env` 文件
2. 添加你的 API Key：
   ```
   VITE_GEMINI_API_KEY=your_api_key_here
   ```

---

# 🧭 Vision | 愿景

FlowSelf 希望成为：

> **你一生都能持续依赖的个人成长操作平台。**

它记录生活、理解知识、反哺思考，并推动你成为更好的自己。

---

# 📄 License | 许可证

MIT License

---

# 🤝 Contributing | 贡献

欢迎提交 Issue 和 Pull Request！

---
