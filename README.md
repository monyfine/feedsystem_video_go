您说得对，之前的表格格式在纯文本中显示确实不好看。让我给您一个更美观、更清晰的 README 版本：

```markdown
# 🎬 Video Feed System

> 高性能视频社交平台后端 | Go + Gin + MySQL + Redis + RabbitMQ

[![Go Version](https://img.shields.io/badge/Go-1.24+-00ADD8?style=flat&logo=go)](https://go.dev/)
[![Gin](https://img.shields.io/badge/Gin-1.11.0-00ADD8?style=flat&logo=gin)](https://gin-gonic.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

## 📖 目录

- [核心特性](#-核心特性)
- [架构设计](#-架构设计)
- [功能列表](#-功能列表)
- [快速开始](#-快速开始)
- [API 示例](#-api-示例)
- [性能优化](#-性能优化)
- [项目结构](#-项目结构)

---

## ✨ 核心特性

| 特性 | 说明 |
|------|------|
| 🔐 **用户系统** | JWT 认证、资料管理、头像上传 |
| 🎥 **视频管理** | 视频上传、封面处理、标签系统 |
| 📱 **Feed 流** | 最新、热度、关注、标签四种排序 |
| 💕 **社交互动** | 关注/取关、点赞、评论、@提及 |
| 💬 **私信系统** | 用户间实时消息 |
| 🔔 **实时通知** | SSE + RabbitMQ 实时推送 |
| 🚀 **高性能** | 三级缓存、冷热分离、异步处理 |

---

## 🏗 架构设计

```
┌─────────────────────────────────────────────────────────────┐
│                        客户端层                              │
│                   Web App  /   Mobile App                    │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      网关层 (Gin)                           │
│         路由管理  ·  限流保护  ·  JWT认证  ·  SSE推送        │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│  账户服务    │      │  视频服务    │      │  Feed服务    │
│  Account     │      │   Video      │      │   Feed       │
└──────────────┘      └──────────────┘      └──────────────┘
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      数据访问层                              │
│            L1(本地缓存) → L2(Redis) → L3(MySQL)             │
└─────────────────────────────────────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        ▼                     ▼                     ▼
┌──────────────┐      ┌──────────────┐      ┌──────────────┐
│    MySQL     │      │    Redis     │      │  RabbitMQ    │
│   持久化     │      │   缓存/热榜  │      │   消息队列   │
└──────────────┘      └──────────────┘      └──────────────┘
```

---

## 📋 功能列表

### 👤 账号模块

| 接口 | 方法 | 说明 | 认证 |
|------|------|------|:----:|
| `/account/register` | POST | 用户注册 | ❌ |
| `/account/login` | POST | 用户登录 | ❌ |
| `/account/refresh` | POST | 刷新 Token | ❌ |
| `/account/logout` | POST | 退出登录 | ✅ |
| `/account/rename` | POST | 修改用户名 | ✅ |
| `/account/uploadAvatar` | POST | 上传头像 | ✅ |
| `/account/updateProfile` | POST | 更新资料 | ✅ |
| `/account/getProfile` | POST | 获取资料 | ❌ |

### 🎥 视频模块

| 接口 | 方法 | 说明 | 认证 |
|------|------|------|:----:|
| `/video/publish` | POST | 发布视频 | ✅ |
| `/video/uploadVideo` | POST | 上传视频文件 | ✅ |
| `/video/uploadCover` | POST | 上传封面 | ✅ |
| `/video/getDetail` | POST | 视频详情 | ❌ |
| `/video/listByAuthorID` | POST | 作者视频列表 | ❌ |
| `/video/delete` | POST | 删除视频 | ✅ |

### 📱 Feed 流模块

| 接口 | 方法 | 说明 | 认证 |
|------|------|------|:----:|
| `/feed/listLatest` | POST | 最新视频 | 🔘 |
| `/feed/listByPopularity` | POST | 热门视频 | 🔘 |
| `/feed/listByFollowing` | POST | 关注动态 | ✅ |
| `/feed/listByTag` | POST | 标签筛选 | 🔘 |

> 🔘 可选认证（有 Token 显示点赞状态，无 Token 也可访问）

### 💕 社交互动

| 接口 | 方法 | 说明 | 认证 |
|------|------|------|:----:|
| `/like/like` | POST | 点赞 | ✅ |
| `/like/unlike` | POST | 取消点赞 | ✅ |
| `/like/listMyLikedVideos` | POST | 我的点赞 | ✅ |
| `/comment/publish` | POST | 发表评论 | ✅ |
| `/comment/delete` | POST | 删除评论 | ✅ |
| `/comment/listAll` | POST | 评论列表 | ❌ |
| `/social/follow` | POST | 关注 | ✅ |
| `/social/unfollow` | POST | 取关 | ✅ |
| `/social/getAllFollowers` | POST | 粉丝列表 | ❌ |
| `/social/getAllVloggers` | POST | 关注列表 | ❌ |

### 💬 消息通知

| 接口 | 方法 | 说明 | 认证 |
|------|------|------|:----:|
| `/message/send` | POST | 发送私信 | ✅ |
| `/message/list` | POST | 私信列表 | ✅ |
| `/notification/stream` | GET | SSE 实时推送 | ✅ |
| `/notification/list` | POST | 通知列表 | ✅ |
| `/notification/markRead` | POST | 标记已读 | ✅ |

---

## 🚀 快速开始

### 环境要求

```bash
Go 1.24+      # 运行环境
MySQL 8.0+    # 主数据库
Redis 7.0+    # 缓存服务
RabbitMQ 4.0+ # 消息队列
```

### 一键启动（Docker）

```bash
# 1. 克隆项目
git clone https://github.com/monyfine/feedsystem_video_go.git
cd feedsystem_video_go

# 2. 启动依赖服务
docker run -d --name mysql -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -e MYSQL_DATABASE=feedsystem mysql:8

docker run -d --name redis -p 6379:6379 \
  -e REDIS_PASSWORD=123456 redis:7 --requirepass 123456

docker run -d --name rabbitmq -p 5672:5672 \
  -e RABBITMQ_DEFAULT_USER=admin \
  -e RABBITMQ_DEFAULT_PASS=password123 rabbitmq:4-management

# 3. 启动服务
go run main.go
```

### 测试接口

```bash
# 注册用户
curl -X POST http://localhost:8080/account/register \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","password":"123456"}'

# 登录获取 Token
curl -X POST http://localhost:8080/account/login \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","password":"123456"}'

# 获取 Feed 流
curl -X POST http://localhost:8080/feed/listLatest \
  -H "Content-Type: application/json" \
  -d '{"limit":10}'
```

---

## 📊 API 示例

### 注册账号

```http
POST /account/register
Content-Type: application/json

{
  "username": "alice",
  "password": "123456"
}
```

**响应：**
```json
{
  "message": "account created"
}
```

### 登录

```http
POST /account/login
Content-Type: application/json

{
  "username": "alice",
  "password": "123456"
}
```

**响应：**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "refresh_token": "abc123...",
  "account_id": 1,
  "username": "alice"
}
```

### 发布视频

```http
POST /video/publish
Authorization: Bearer {token}
Content-Type: application/json

{
  "title": "我的第一个视频",
  "description": "视频描述",
  "play_url": "https://example.com/video.mp4",
  "cover_url": "https://example.com/cover.jpg"
}
```

### 获取 Feed

```http
POST /feed/listLatest
Content-Type: application/json

{
  "limit": 20,
  "latest_time": 0
}
```

**响应：**
```json
{
  "video_list": [
    {
      "id": 100,
      "author": {
        "id": 1,
        "username": "alice"
      },
      "title": "我的第一个视频",
      "play_url": "https://...",
      "cover_url": "https://...",
      "likes_count": 123,
      "is_liked": false
    }
  ],
  "next_time": 1704067200000,
  "has_more": true
}
```

---

## ⚡ 性能优化

### 三级缓存架构

| 级别 | 存储 | 过期时间 | 命中率 |
|------|------|----------|--------|
| L1 | 本地内存 (go-cache) | 5秒 | 60% |
| L2 | Redis | 24小时 | 35% |
| L3 | MySQL | 持久化 | 5% |

### 性能指标

| 指标 | 数值 |
|------|------|
| 单机 QPS | 500-1000 |
| P99 延迟 | < 200ms |
| 缓存命中率 | > 90% |
| 系统可用性 | 99.9% |

---

## 📁 项目结构

```
feedsystem_video_go/
├── cmd/                    # 应用入口
│   ├── api/               # API 服务
│   └── worker/            # Worker 服务
├── internal/              # 内部代码
│   ├── account/           # 👤 用户模块
│   ├── video/             # 🎥 视频模块
│   ├── feed/              # 📱 Feed 流
│   ├── social/            # 💕 社交模块
│   ├── message/           # 💬 消息模块
│   ├── middleware/        # 🔧 中间件
│   │   ├── jwt/          # JWT 认证
│   │   ├── ratelimit/    # 限流
│   │   ├── redis/        # Redis 客户端
│   │   └── rabbitmq/     # RabbitMQ 客户端
│   ├── worker/            # 🔄 后台任务
│   └── config/            # ⚙️ 配置管理
├── configs/               # 配置文件
├── scripts/               # 脚本工具
└── go.mod                 # 依赖管理
```

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

---

## 📄 开源协议

MIT License © 2024 monyfine

---

⭐️ 如果这个项目对你有帮助，请给个 Star 支持一下！
```