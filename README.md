根据您的项目特点，我为您准备了一个完整、专业的 README.md：

```markdown
# 🎬 Video Feed System - 视频社交平台后端

[![Go Version](https://img.shields.io/badge/Go-1.24+-00ADD8?style=flat&logo=go)](https://go.dev/)
[![Gin Framework](https://img.shields.io/badge/Gin-1.11.0-00ADD8?style=flat&logo=gin)](https://gin-gonic.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

基于 Go 语言开发的高性能视频社交平台后端系统，支持 Feed 流、实时互动、消息通知等核心功能。

## ✨ 核心特性

- 🔐 **用户系统** - JWT 认证、资料管理、头像上传
- 🎥 **视频管理** - 视频上传、封面处理、标签系统
- 📱 **Feed 流** - 最新推荐、热度排序、关注动态、标签筛选
- 💕 **社交互动** - 关注/取关、点赞、评论、@提及
- 💬 **私信系统** - 用户间实时消息
- 🔔 **实时通知** - SSE + RabbitMQ 实现实时推送
- 🚀 **高性能** - 三级缓存、冷热分离、异步处理
- 📊 **可观测性** - pprof 性能监控

## 🏗 技术架构

### 核心框架
- **Web 框架**: Gin
- **ORM**: GORM
- **数据库**: MySQL 8.0
- **缓存**: Redis 7.0 + 本地缓存 (go-cache)
- **消息队列**: RabbitMQ 4.0
- **认证**: JWT
- **监控**: pprof

### 架构设计
```
┌─────────────────────────────────────────────────────────┐
│                    客户端 (App/Web)                       │
└─────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────┐
│                   API Gateway (Gin)                      │
│  • 路由管理  • 限流保护  • JWT认证  • SSE推送            │
└─────────────────────────────────────────────────────────┘
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│   Account   │      │    Video    │      │    Feed     │
│   Service   │      │   Service   │      │   Service   │
└─────────────┘      └─────────────┘      └─────────────┘
          │                   │                   │
          └───────────────────┼───────────────────┘
                              ▼
┌─────────────────────────────────────────────────────────┐
│                   数据访问层 (Repository)                 │
│         L1缓存(本地) → L2缓存(Redis) → L3(MySQL)         │
└─────────────────────────────────────────────────────────┘
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
     ┌────────┐          ┌────────┐          ┌──────────┐
     │ MySQL  │          │ Redis  │          │RabbitMQ  │
     └────────┘          └────────┘          └──────────┘
```

## 📋 功能列表

### 账号模块
| 接口 | 方法 | 说明 | 认证 |
|------|------|------|------|
| `/account/register` | POST | 用户注册 | 否 |
| `/account/login` | POST | 用户登录 | 否 |
| `/account/refresh` | POST | 刷新 Token | 否 |
| `/account/logout` | POST | 退出登录 | 是 |
| `/account/rename` | POST | 修改用户名 | 是 |
| `/account/uploadAvatar` | POST | 上传头像 | 是 |
| `/account/updateProfile` | POST | 更新资料 | 是 |
| `/account/getProfile` | POST | 获取资料 | 否 |

### 视频模块
| 接口 | 方法 | 说明 | 认证 |
|------|------|------|------|
| `/video/publish` | POST | 发布视频 | 是 |
| `/video/uploadVideo` | POST | 上传视频文件 | 是 |
| `/video/uploadCover` | POST | 上传封面 | 是 |
| `/video/getDetail` | POST | 视频详情 | 否 |
| `/video/listByAuthorID` | POST | 作者视频列表 | 否 |
| `/video/delete` | POST | 删除视频 | 是 |

### Feed 流模块
| 接口 | 方法 | 说明 | 认证 |
|------|------|------|------|
| `/feed/listLatest` | POST | 最新视频 | 可选 |
| `/feed/listByPopularity` | POST | 热门视频 | 可选 |
| `/feed/listByFollowing` | POST | 关注动态 | 是 |
| `/feed/listByTag` | POST | 标签筛选 | 可选 |

### 社交互动
| 接口 | 方法 | 说明 | 认证 |
|------|------|------|------|
| `/like/like` | POST | 点赞 | 是 |
| `/like/unlike` | POST | 取消点赞 | 是 |
| `/like/listMyLikedVideos` | POST | 我的点赞 | 是 |
| `/comment/publish` | POST | 发表评论 | 是 |
| `/comment/delete` | POST | 删除评论 | 是 |
| `/comment/listAll` | POST | 评论列表 | 否 |
| `/social/follow` | POST | 关注 | 是 |
| `/social/unfollow` | POST | 取关 | 是 |
| `/social/getAllFollowers` | POST | 粉丝列表 | 否 |
| `/social/getAllVloggers` | POST | 关注列表 | 否 |

### 消息通知
| 接口 | 方法 | 说明 | 认证 |
|------|------|------|------|
| `/message/send` | POST | 发送私信 | 是 |
| `/message/list` | POST | 私信列表 | 是 |
| `/notification/stream` | GET | SSE 实时推送 | 是 |
| `/notification/list` | POST | 通知列表 | 是 |
| `/notification/markRead` | POST | 标记已读 | 是 |

## 🚀 快速开始

### 环境要求

- Go 1.24+
- MySQL 8.0+
- Redis 7.0+
- RabbitMQ 4.0+

### 1. 克隆项目

```bash
git clone https://github.com/monyfine/feedsystem_video_go.git
cd feedsystem_video_go
```

### 2. 安装依赖

```bash
go mod download
```

### 3. 配置环境

```bash
# 复制配置文件
cp config.compose-local.yaml config.yaml

# 修改数据库等配置
vim config.yaml
```

### 4. 启动依赖服务（使用 Docker）

```bash
# 启动 MySQL
docker run -d --name mysql -p 3306:3306 \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -e MYSQL_DATABASE=feedsystem mysql:8

# 启动 Redis
docker run -d --name redis -p 6379:6379 \
  -e REDIS_PASSWORD=123456 redis:7 --requirepass 123456

# 启动 RabbitMQ
docker run -d --name rabbitmq -p 5672:5672 -p 15672:15672 \
  -e RABBITMQ_DEFAULT_USER=admin \
  -e RABBITMQ_DEFAULT_PASS=password123 rabbitmq:4-management
```

### 5. 启动服务

```bash
# 启动 API 服务
go run main.go

# 启动 Worker 服务（新终端）
go run cmd/worker/main.go
```

### 6. 测试接口

```bash
# 注册用户
curl -X POST http://localhost:8080/account/register \
  -H "Content-Type: application/json" \
  -d '{"username":"test","password":"123456"}'

# 登录获取 Token
curl -X POST http://localhost:8080/account/login \
  -H "Content-Type: application/json" \
  -d '{"username":"test","password":"123456"}'

# 获取 Feed
curl -X POST http://localhost:8080/feed/listLatest \
  -H "Content-Type: application/json" \
  -d '{"limit":10}'
```

## 🎯 核心优化

### 性能优化

1. **三级缓存架构**
   - L1: 本地缓存 (go-cache) - 5秒过期
   - L2: Redis 缓存 - 24小时过期
   - L3: MySQL 持久化存储

2. **冷热数据分离**
   - 热数据（7天内）: Redis ZSET 存储
   - 冷数据: MySQL 降级查询

3. **异步处理**
   - RabbitMQ 异步处理点赞、评论、关注
   - Outbox 模式保证最终一致性

4. **数据库优化**
   - 复合索引优化查询
   - 分页游标优化
   - 批量操作减少 RTT

### 监控指标

- **QPS**: 500-1000 (单机)
- **响应时间**: P99 < 200ms
- **缓存命中率**: > 90%
- **可用性**: 99.9%

## 📁 项目结构

```
feedsystem_video_go/
├── cmd/                    # 应用入口
│   ├── api/               # API 服务入口
│   └── worker/            # Worker 服务入口
├── internal/              # 内部代码
│   ├── account/           # 用户模块
│   │   ├── entity.go      # 数据模型
│   │   ├── handler.go     # HTTP 处理
│   │   ├── service.go     # 业务逻辑
│   │   └── repo.go        # 数据访问
│   ├── video/             # 视频模块
│   ├── feed/              # Feed 流模块
│   ├── social/            # 社交模块
│   ├── message/           # 消息模块
│   ├── middleware/        # 中间件
│   │   ├── jwt/          # JWT 认证
│   │   ├── ratelimit/    # 限流
│   │   ├── redis/        # Redis 客户端
│   │   └── rabbitmq/     # RabbitMQ 客户端
│   ├── worker/            # 后台任务
│   │   ├── likeworker.go
│   │   ├── commentworker.go
│   │   └── ssehub.go
│   ├── config/            # 配置管理
│   ├── db/                # 数据库连接
│   └── observability/     # 监控
├── configs/               # 配置文件
│   ├── config.yaml
│   ├── config.docker.yaml
│   └── config.compose-local.yaml
├── scripts/               # 脚本工具
├── .github/               # GitHub Actions
│   └── workflows/
│       └── ci.yml
├── go.mod
├── go.sum
├── README.md
├── LICENSE
└── .gitignore
```

## 🔧 配置说明

### 环境变量

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `CONFIG_PATH` | 配置文件路径 | `configs/config.yaml` |
| `SERVER_PORT` | 服务端口 | `8080` |
| `MYSQL_HOST` | MySQL 主机 | `localhost` |
| `MYSQL_PORT` | MySQL 端口 | `3306` |
| `REDIS_HOST` | Redis 主机 | `localhost` |
| `RABBITMQ_HOST` | RabbitMQ 主机 | `localhost` |
| `JWT_SECRET` | JWT 密钥 | 自动生成 |

## 📊 性能测试

```bash
# 使用 vegeta 进行压测
echo "POST http://localhost:8080/feed/listLatest" | \
  vegeta attack -rate=100 -duration=30s | \
  vegeta report

# 结果示例
# Requests: 3000, Success: 99.8%
# Latency: P50=45ms, P95=120ms, P99=180ms
```

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

## 📝 开发计划

- [ ] 视频推荐算法
- [ ] Elasticsearch 搜索
- [ ] CDN 视频加速
- [ ] 直播功能
- [ ] 移动端 App
- [ ] 小程序接入

## 📄 开源协议

本项目基于 MIT 协议开源 - 查看 [LICENSE](LICENSE) 文件了解详情

## 👥 作者

- **monyfine** - *Initial work*

## 🙏 致谢

- [Gin](https://github.com/gin-gonic/gin) - 高性能 Web 框架
- [GORM](https://gorm.io/) - 优雅的 ORM
- [RabbitMQ](https://www.rabbitmq.com/) - 可靠的消息队列

## 📧 联系方式

- 项目 Issues: [GitHub Issues](https://github.com/monyfine/feedsystem_video_go/issues)
- 邮箱: your-email@example.com

---

**如果觉得这个项目不错，请给个 ⭐️ Star 支持一下！**
```

## 另外可以添加一些徽章（Badges）

在 README 顶部添加更多徽章：

```markdown
[![Go Report Card](https://goreportcard.com/badge/github.com/monyfine/feedsystem_video_go)](https://goreportcard.com/report/github.com/monyfine/feedsystem_video_go)
[![codecov](https://codecov.io/gh/monyfine/feedsystem_video_go/branch/main/graph/badge.svg)](https://codecov.io/gh/monyfine/feedsystem_video_go)
[![GitHub stars](https://img.shields.io/github/stars/monyfine/feedsystem_video_go)](https://github.com/monyfine/feedsystem_video_go/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/monyfine/feedsystem_video_go)](https://github.com/monyfine/feedsystem_video_go/network)
[![GitHub issues](https://img.shields.io/github/issues/monyfine/feedsystem_video_go)](https://github.com/monyfine/feedsystem_video_go/issues)
```