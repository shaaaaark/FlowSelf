# 心流统一后台架构设计文档

## 一、架构原则

### 1.1 核心变更
- **三端共用统一后台接口**：Web、移动、桌面通过相同 API 契约交互
- **单一数据源**：后端统一管理数据存储、权限、同步逻辑
- **离线优先**：桌面端本地缓存 + 最终一致性同步
- **接口版本化**：支持客户端渐进式升级

### 1.2 架构优势
- 业务逻辑集中，避免多端重复实现
- 权限控制统一，安全性可控
- 数据一致性由后端保证
- 客户端轻量化，专注交互体验

---

## 二、技术栈选型

### 2.1 后端服务
```
数据层: Supabase (PostgreSQL + Realtime + pgvector + Auth)
边缘计算: Cloudflare Workers (API 网关、轻量业务逻辑)
文件存储: Cloudflare R2
任务队列: Redis + Bull / Cloudflare Queues
工作流引擎: n8n (自托管)
监控: Sentry + Grafana
```

### 2.2 客户端
```
Web: Next.js 14 + React + TypeScript
移动: React Native + Expo
桌面: Tauri + React + SQLite (本地缓存)
共享层: Monorepo (Turbo) + 统一 SDK
```

### 2.3 接口协议
```
REST API: 主要业务接口
GraphQL: 复杂查询场景（可选）
WebSocket: Realtime 订阅 (Supabase Realtime)
gRPC: 内部服务通信（可选）
```

---

## 三、后端服务职责划分

### 3.1 核心服务模块

#### 认证服务 (Auth Service)
- 用户注册、登录、登出
- 第三方登录（Google、GitHub）
- Token 管理（JWT）
- 会话管理
- 设备管理（多端登录控制）

#### 数据服务 (Data Service)
- Inbox 记录 CRUD
- 笔记管理
- 标签管理
- 媒体文件上传/下载
- 数据软删除与恢复

#### 同步服务 (Sync Service)
- 增量同步
- 冲突检测与解决
- 版本控制
- 操作日志
- 离线队列处理

#### 知识服务 (Knowledge Service)
- 笔记向量化
- 语义检索
- 问答卡片生成
- 复习算法（SM-2/FSRS）
- 知识图谱构建

#### 生活记录服务 (Life Service)
- 情绪记录
- 习惯追踪
- 打卡统计
- 数据可视化聚合

#### 反思服务 (Reflection Service)
- 每日问题推送
- 回答记录
- 历史回顾
- 趋势分析

#### 工作流服务 (Workflow Service)
- 文档解析触发
- AI 任务调度
- 批量处理
- 定时任务

#### 备份服务 (Backup Service)
- 全量/增量备份
- 数据导出（JSON/Markdown）
- 数据导入
- 备份恢复

#### 通知服务 (Notification Service)
- Push 通知
- 邮件通知
- 系统通知
- 通知偏好管理

#### 隐私服务 (Privacy Service)
- Work Mode 切换
- 数据标记（private/work）
- 数据脱敏
- 合规处理（GDPR）

---

## 四、API 设计规范

### 4.1 RESTful 路由结构

```
/api/v1/auth/*              # 认证相关
/api/v1/users/*             # 用户管理
/api/v1/inbox/*             # Inbox 记录
/api/v1/notes/*             # 笔记管理
/api/v1/knowledge/*         # 知识处理
/api/v1/life/*              # 生活记录
/api/v1/reflection/*        # 反思相关
/api/v1/sync/*              # 同步接口
/api/v1/backup/*            # 备份恢复
/api/v1/notifications/*     # 通知管理
/api/v1/media/*             # 媒体文件
```

### 4.2 统一响应格式

```typescript
// 成功响应
{
  "success": true,
  "data": { ... },
  "meta": {
    "timestamp": "2025-11-22T10:00:00Z",
    "version": "v1"
  }
}

// 错误响应
{
  "success": false,
  "error": {
    "code": "CONFLICT_ERROR",
    "message": "数据冲突，请刷新后重试",
    "details": { ... },
    "recoverable": true
  },
  "meta": {
    "timestamp": "2025-11-22T10:00:00Z",
    "version": "v1"
  }
}
```

### 4.3 错误码规范

```
1xxx - 认证错误
  1001: TOKEN_EXPIRED
  1002: INVALID_CREDENTIALS
  1003: UNAUTHORIZED

2xxx - 业务错误
  2001: RESOURCE_NOT_FOUND
  2002: VALIDATION_ERROR
  2003: CONFLICT_ERROR
  2004: QUOTA_EXCEEDED

3xxx - 同步错误
  3001: SYNC_CONFLICT
  3002: VERSION_MISMATCH
  3003: OFFLINE_QUEUE_FULL

4xxx - 系统错误
  4001: INTERNAL_ERROR
  4002: SERVICE_UNAVAILABLE
  4003: RATE_LIMIT_EXCEEDED
```

### 4.4 版本控制策略

```
URL 版本: /api/v1/*, /api/v2/*
Header 版本: X-API-Version: 1.0
客户端版本: X-Client-Version: 0.1.0
最低支持版本: X-Min-Client-Version: 0.1.0
```

---

## 五、同步与冲突解决机制

### 5.1 数据版本字段

每个可同步实体包含：
```typescript
{
  id: string;              // UUID
  user_id: string;
  created_at: timestamp;
  updated_at: timestamp;
  version: number;         // 乐观锁版本号
  operation_id: string;    // 操作唯一标识
  device_id: string;       // 设备标识
  synced: boolean;         // 是否已同步
  deleted: boolean;        // 软删除标记
}
```

### 5.2 同步流程

#### 客户端 → 服务端（上传）
```
1. 客户端收集本地未同步操作
2. 批量提交到 /api/v1/sync/push
3. 服务端验证 version 字段
4. 检测冲突：
   - 无冲突：应用变更，返回新 version
   - 有冲突：返回冲突详情与服务端最新数据
5. 客户端处理冲突响应
```

#### 服务端 → 客户端（下载）
```
1. 客户端请求 /api/v1/sync/pull?since=<timestamp>
2. 服务端返回增量变更
3. 客户端应用变更到本地数据库
4. 更新本地同步时间戳
```

### 5.3 冲突解决策略

#### 策略 1: Last Write Wins (LWW)
```
比较 updated_at 时间戳，最新的覆盖旧的
适用场景：Inbox 记录、情绪日志
```

#### 策略 2: 版本号优先
```
比较 version 字段，版本号大的优先
适用场景：笔记内容、习惯配置
```

#### 策略 3: 操作合并
```
保留双方操作，生成合并版本
适用场景：标签添加、打卡记录
```

#### 策略 4: 用户选择
```
返回冲突给客户端，由用户决定
适用场景：重要笔记内容冲突
```

### 5.4 冲突检测 API

```typescript
POST /api/v1/sync/push
Request:
{
  "operations": [
    {
      "operation_id": "uuid",
      "entity_type": "note",
      "entity_id": "uuid",
      "action": "update",
      "version": 5,
      "data": { ... },
      "timestamp": "2025-11-22T10:00:00Z"
    }
  ]
}

Response (有冲突):
{
  "success": false,
  "conflicts": [
    {
      "operation_id": "uuid",
      "entity_id": "uuid",
      "conflict_type": "VERSION_MISMATCH",
      "client_version": 5,
      "server_version": 7,
      "server_data": { ... },
      "resolution_strategy": "USER_CHOICE"
    }
  ]
}
```

---

## 六、离线支持设计

### 6.1 桌面端本地架构

```
┌─────────────────────────────────┐
│      Tauri 应用层 (React)        │
├─────────────────────────────────┤
│      同步管理器 (Rust)           │
│  - 操作队列                      │
│  - 冲突解决                      │
│  - 重试机制                      │
├─────────────────────────────────┤
│      本地数据库 (SQLite)         │
│  - 完整数据副本                  │
│  - 操作日志表                    │
│  - 同步状态表                    │
└─────────────────────────────────┘
```

### 6.2 操作队列表结构

```sql
CREATE TABLE operation_queue (
  id TEXT PRIMARY KEY,
  entity_type TEXT NOT NULL,
  entity_id TEXT NOT NULL,
  action TEXT NOT NULL, -- create/update/delete
  data TEXT NOT NULL,   -- JSON
  version INTEGER,
  timestamp INTEGER NOT NULL,
  synced INTEGER DEFAULT 0,
  retry_count INTEGER DEFAULT 0,
  error TEXT
);
```

### 6.3 离线操作流程

```
1. 用户操作 → 立即写入本地 SQLite
2. 操作记录到 operation_queue
3. 后台同步线程定期检查网络
4. 网络可用时批量上传队列
5. 处理服务端响应（成功/冲突/失败）
6. 更新本地数据与队列状态
```

---

## 七、数据库表结构设计

### 7.1 核心表

```sql
-- 用户表（Supabase Auth 自带，扩展字段）
CREATE TABLE user_profiles (
  id UUID PRIMARY KEY REFERENCES auth.users,
  display_name TEXT,
  avatar_url TEXT,
  work_mode BOOLEAN DEFAULT FALSE,
  timezone TEXT DEFAULT 'UTC',
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  version INTEGER DEFAULT 1
);

-- Inbox 记录表
CREATE TABLE inbox_items (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  content TEXT NOT NULL,
  archived BOOLEAN DEFAULT FALSE,
  is_private BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  version INTEGER DEFAULT 1,
  device_id TEXT,
  deleted BOOLEAN DEFAULT FALSE
);

-- 笔记表
CREATE TABLE notes (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  title TEXT NOT NULL,
  content TEXT,
  tags TEXT[],
  is_private BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW(),
  version INTEGER DEFAULT 1,
  device_id TEXT,
  deleted BOOLEAN DEFAULT FALSE
);

-- 同步日志表
CREATE TABLE sync_logs (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  device_id TEXT NOT NULL,
  operation_id TEXT NOT NULL,
  entity_type TEXT NOT NULL,
  entity_id UUID NOT NULL,
  action TEXT NOT NULL,
  synced_at TIMESTAMPTZ DEFAULT NOW(),
  conflict BOOLEAN DEFAULT FALSE
);

-- 设备表
CREATE TABLE devices (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES auth.users NOT NULL,
  device_id TEXT UNIQUE NOT NULL,
  device_name TEXT,
  device_type TEXT, -- web/mobile/desktop
  last_sync_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 7.2 索引策略

```sql
-- 用户查询优化
CREATE INDEX idx_inbox_user_created ON inbox_items(user_id, created_at DESC);
CREATE INDEX idx_notes_user_updated ON notes(user_id, updated_at DESC);

-- 同步查询优化
CREATE INDEX idx_inbox_user_updated ON inbox_items(user_id, updated_at) WHERE deleted = FALSE;
CREATE INDEX idx_sync_logs_user_device ON sync_logs(user_id, device_id, synced_at DESC);

-- 全文搜索
CREATE INDEX idx_notes_content_fts ON notes USING gin(to_tsvector('english', content));
```

### 7.3 RLS 策略

```sql
-- Inbox 行级安全
ALTER TABLE inbox_items ENABLE ROW LEVEL SECURITY;

CREATE POLICY "用户只能访问自己的记录"
  ON inbox_items FOR ALL
  USING (auth.uid() = user_id);

-- Work Mode 过滤
CREATE POLICY "工作模式下隐藏私人数据"
  ON inbox_items FOR SELECT
  USING (
    auth.uid() = user_id AND
    (is_private = FALSE OR
     (SELECT work_mode FROM user_profiles WHERE id = auth.uid()) = FALSE)
  );
```

---

## 八、实时通信设计

### 8.1 Realtime 订阅

```typescript
// 客户端订阅
const channel = supabase
  .channel('user-changes')
  .on('postgres_changes', {
    event: '*',
    schema: 'public',
    table: 'inbox_items',
    filter: `user_id=eq.${userId}`
  }, (payload) => {
    handleRealtimeUpdate(payload);
  })
  .subscribe();
```

### 8.2 推送通知

```typescript
POST /api/v1/notifications/send
{
  "user_id": "uuid",
  "type": "daily_reflection",
  "title": "今日反思",
  "body": "今天有什么值得记录的吗？",
  "data": {
    "question_id": "uuid"
  },
  "channels": ["push", "email"],
  "respect_work_mode": true
}
```

---

## 九、备份与恢复

### 9.1 备份接口

```typescript
POST /api/v1/backup/export
Response:
{
  "backup_id": "uuid",
  "download_url": "https://r2.../backup-20251122.json",
  "expires_at": "2025-11-23T10:00:00Z",
  "size_bytes": 1048576,
  "entities": {
    "inbox_items": 150,
    "notes": 45,
    "habits": 10
  }
}
```

### 9.2 备份文件格式

```json
{
  "version": "1.0",
  "exported_at": "2025-11-22T10:00:00Z",
  "user_id": "uuid",
  "data": {
    "inbox_items": [...],
    "notes": [...],
    "habits": [...],
    "mood_logs": [...]
  },
  "metadata": {
    "total_entities": 205,
    "checksum": "sha256..."
  }
}
```

### 9.3 恢复接口

```typescript
POST /api/v1/backup/import
Content-Type: multipart/form-data

{
  "file": <backup.json>,
  "strategy": "merge" | "replace",
  "conflict_resolution": "keep_newer" | "keep_existing"
}
```

---

## 十、监控与可观测性

### 10.1 关键指标

```
业务指标:
- 日活用户数 (DAU)
- 记录创建数/天
- 同步成功率
- 冲突发生率

技术指标:
- API 响应时间 (P50/P95/P99)
- 错误率
- 数据库连接池使用率
- 向量检索延迟
```

### 10.2 告警规则

```
- API 错误率 > 1% 持续 5 分钟
- 同步失败率 > 5% 持续 10 分钟
- 数据库响应时间 > 500ms (P95)
- 磁盘使用率 > 80%
```

---

## 十一、部署架构

### 11.1 生产环境

```
┌─────────────────────────────────────────┐
│         Cloudflare (CDN + Workers)       │
│  - 静态资源                              │
│  - API 网关                              │
│  - 边缘缓存                              │
└────────────┬────────────────────────────┘
             │
┌────────────▼────────────────────────────┐
│         Supabase                         │
│  - PostgreSQL (主数据库)                 │
│  - Realtime (WebSocket)                  │
│  - Auth (认证)                           │
│  - Storage (临时文件)                    │
└────────────┬────────────────────────────┘
             │
┌────────────▼────────────────────────────┐
│         Cloudflare R2                    │
│  - 媒体文件存储                          │
│  - 备份文件存储                          │
└──────────────────────────────────────────┘

┌──────────────────────────────────────────┐
│         n8n (工作流引擎)                  │
│  - 文档解析                              │
│  - AI 任务调度                           │
│  - 定时任务                              │
└──────────────────────────────────────────┘
```

### 11.2 开发环境

```
本地 Docker Compose:
- Supabase (本地实例)
- PostgreSQL
- Redis
- n8n
```

---

## 十二、安全策略

### 12.1 认证
- JWT Token (短期有效)
- Refresh Token (长期有效)
- 设备指纹验证
- 异常登录检测

### 12.2 授权
- RLS (Row Level Security)
- API 级别权限检查
- Work Mode 数据隔离

### 12.3 数据安全
- 传输加密 (HTTPS/WSS)
- 敏感字段加密存储
- 定期备份
- 审计日志

---

## 十三、性能优化

### 13.1 缓存策略
```
L1: 客户端内存缓存 (5 分钟)
L2: Cloudflare Workers KV (1 小时)
L3: PostgreSQL 查询缓存
```

### 13.2 数据库优化
- 连接池管理
- 慢查询监控
- 索引优化
- 分区表（大数据量时）

### 13.3 API 优化
- 批量接口
- GraphQL DataLoader
- 分页与游标
- 字段过滤

---

## 十四、下一步行动

1. 评审统一后台架构方案
2. 确认同步与冲突解决策略
3. 生成完整 OpenAPI 规范
4. 搭建后端服务基础框架
5. 实现核心同步逻辑
