# 🎬 FeedSystem Video Go

![Go Version](https://img.shields.io/badge/Go-1.20+-00ADD8?style=flat&logo=go)
![Gin](https://img.shields.io/badge/Framework-Gin-00ADD8?style=flat)
![MySQL](https://img.shields.io/badge/Database-MySQL-4479A1?style=flat&logo=mysql)
![Redis](https://img.shields.io/badge/Cache-Redis-DC382D?style=flat&logo=redis)
![RabbitMQ](https://img.shields.io/badge/MQ-RabbitMQ-FF6600?style=flat&logo=rabbitmq)

**FeedSystem Video Go** 是一个基于 Go 语言开发的高性能短视频/信息流后端系统。项目采用微服务架构思想，实现了类似抖音/TikTok 的核心业务逻辑，包括视频发布、个性化信息流推荐、社交互动（点赞、评论、关注）、实时消息通知等功能。

系统在设计上充分考虑了**高并发**与**高可用**，引入了多级缓存、消息队列异步解耦、可靠消息投递（Outbox 模式）以及服务降级等高级特性。

---

## ✨ 核心功能

- 👤 **用户系统**：注册登录、JWT 鉴权（无感刷新）、个人主页、头像上传。
- 📹 **视频系统**：视频与封面上传、视频发布、基于标签（Tag）的视频分类。
- 🔄 **信息流系统 (Feed)**：
  - **最新流**：基于时间戳游标的分页，冷热数据分离。
  - **热门流**：基于 Redis ZSET 的滑动时间窗口热度排行榜。
  - **关注流**：拉模式获取关注创作者的最新动态。
  - **点赞流**：基于点赞数的高效游标分页。
- 💬 **互动系统**：视频点赞、取消点赞、评论发布与删除、评论 `@` 提及功能。
- 🤝 **社交系统**：关注、取消关注、粉丝列表、关注列表、私信（Direct Message）。
- 🔔 **实时通知 (SSE)**：基于 Server-Sent Events 的轻量级实时消息推送（点赞、评论、关注、提及提醒）。

---

## 🚀 技术亮点 & 架构设计

1. **多级缓存架构 (L1 + L2 + L3)**
   - **L1 本地缓存**：使用 `go-cache` 应对极热点数据访问。
   - **L2 分布式缓存**：使用 Redis 存储热点视频实体和 ZSET 时间线。
   - **L3 数据库**：MySQL 作为持久化层。
   - **防击穿**：结合 `golang.org/x/sync/singleflight` 合并并发请求，保护底层数据库。

2. **异步削峰与解耦 (RabbitMQ)**
   - 点赞、评论、关注、热度更新等高频写操作通过 RabbitMQ 异步化处理，极大提升接口响应速度。
   - 配置了 **死信队列 (DLX)** 和重试机制，保障消息不丢失。
   - 具备**优雅降级**能力：当 MQ 或 Redis 宕机时，系统可自动降级为直接操作 MySQL，保障核心业务可用。

3. **可靠消息投递 (Outbox Pattern)**
   - 视频发布时采用**发件箱模式**，利用 MySQL 本地事务保证业务数据与消息记录的一致性，随后由后台 Worker 轮询投递到 MQ，确保时间线更新的最终一致性。

4. **高性能分页与限流**
   - 摒弃传统的 `OFFSET/LIMIT`，全面采用**游标分页 (Cursor-based Pagination)**，解决深度分页性能衰减问题。
   - 基于 Redis + Lua 脚本实现了细粒度的**接口限流**（支持按 IP 或按用户 ID 限流）。

---

## 🛠️ 技术栈

- **编程语言**：Golang
- **Web 框架**：Gin
- **ORM 框架**：GORM
- **数据库**：MySQL 8.0+
- **缓存**：Redis 7.0+
- **消息队列**：RabbitMQ
- **认证**：JWT (JSON Web Token) + bcrypt 密码哈希
- **可观测性**：内置 `net/http/pprof` 性能分析端点

---

## 📂 项目结构

```text
.
├── cmd/
│   ├── main.go        # API Server 入口
│   └── worker/main.go     # 异步任务 Worker 入口
├── configs/               # 配置文件目录
├── internal/
│   ├── account/           # 用户账号模块
│   ├── apierror/          # 统一错误处理
│   ├── auth/              # JWT 鉴权逻辑
│   ├── config/            # 配置加载
│   ├── db/                # 数据库连接与迁移
│   ├── feed/              # 信息流核心逻辑
│   ├── http/              # Gin 路由注册
│   ├── message/           # 私信模块
│   ├── middleware/        # 中间件 (JWT, 限流, Redis, RabbitMQ)
│   ├── observability/     # Pprof 性能监控
│   ├── social/            # 关注/粉丝社交模块
│   ├── video/             # 视频、点赞、评论模块
│   └── worker/            # MQ 消费者、SSE 推送、Outbox 轮询
└── .run/uploads/          # 本地静态资源上传目录 (视频/封面/头像)

(注：根据实际代码结构，入口文件可能位于项目根目录下的不同 main.go 中，分为 API 和 Worker 两个进程)

🚦 快速开始

1. 环境准备

确保你的本地或服务器已安装并运行以下中间件：

  - MySQL (>= 8.0)
  - Redis (>= 6.0)
  - RabbitMQ (>= 3.8)

2. 配置文件

项目默认读取 configs/config.yaml，你也可以通过环境变量覆盖配置。参考配置如下：

server:
  port: 8080
database:
  host: "localhost"
  port: 3306
  user: "root"
  password: "your_password"
  dbname: "feedsystem"
redis:
  host: "localhost"
  port: 6379
  password: ""
  db: 0
rabbitmq:
  host: "localhost"
  port: 5672
  username: "guest"
  password: "guest"
observability:
  pprof:
    enabled: true
    api_addr: "localhost:6060"
    worker_addr: "localhost:6061"

3. 启动服务

项目包含两个核心进程：API Server 和 Worker。

启动 API Server： 提供 HTTP 接口服务，处理用户请求。

go run main.go # 运行 API 版本的 main.go

启动 Worker 进程： 消费 RabbitMQ 消息，处理异步任务（点赞、评论、通知等）。

go run worker_main.go # 运行 Worker 版本的 main.go

📖 API 概览

| 模块          | 端点 (Endpoint)                 | 说明                              |
| ----------- | ----------------------------- | ------------------------------- |
| **Account** | `POST /account/register`      | 用户注册                            |
|             | `POST /account/login`         | 用户登录 (返回 Token & Refresh Token) |
|             | `POST /account/updateProfile` | 更新个人资料                          |
| **Video**   | `POST /video/uploadVideo`     | 上传视频文件                          |
|             | `POST /video/publish`         | 发布视频                            |
| **Feed**    | `POST /feed/listLatest`       | 获取最新视频流 (游标分页)                  |
|             | `POST /feed/listByPopularity` | 获取热门视频流                         |
|             | `POST /feed/listByFollowing`  | 获取关注者的视频流                       |
| **Like**    | `POST /like/like`             | 点赞视频                            |
| **Comment** | `POST /comment/publish`       | 发布评论 (支持 `@` 提及)                |
| **Social**  | `POST /social/follow`         | 关注用户                            |
| **Notify**  | `GET /notification/stream`    | SSE 实时消息推送订阅接口                  |

(详细接口文档建议配合 Postman 或 Swagger 使用)

🤝 贡献指南

欢迎提交 Issue 和 Pull Request 来完善这个项目！

1.  Fork 本仓库
2.  创建你的特性分支 (git checkout -b feature/AmazingFeature)
3.  提交你的更改 (git commit -m 'Add some AmazingFeature')
4.  推送到分支 (git push origin feature/AmazingFeature)
5.  开启一个 Pull Request

📄 许可证

本项目采用 MIT License 开源许可证。


### 💡 为什么这份 README 更好？
1. **视觉吸引力**：顶部加入了技术栈的徽章（Shields），让人一眼看出项目使用的技术。
2. **突出亮点**：将你代码里的精髓（Singleflight 防击穿、L1/L2/L3 多级缓存、Outbox 模式、RabbitMQ 降级机制、SSE 推送）提炼成了“技术亮点”，这对于面试官或者其他开发者来说是最具吸引力的部分。
3. **结构清晰**：从功能介绍到架构设计，再到目录结构和启动指南，符合优秀的开源项目文档规范。
4. **双进程说明**：你的代码里有两个 `main.go`（一个是 API，一个是 Worker），我在“快速开始”里特别注明了需要分别启动，避免新人跑不起来。
