<div align="center">
  <img src="./skillhub-logo.svg" alt="SkillHub Logo" width="120" height="120" />
  <h1>SkillHub</h1>
  <p>企业级开源 Agent 技能注册中心 — 发布、发现和管理可复用的技能包</p>
</div>

<div align="center">

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](./LICENSE)
[![Java](https://img.shields.io/badge/java-21-ED8B00?logo=openjdk&logoColor=white)](https://openjdk.org/projects/jdk/21/)
[![React](https://img.shields.io/badge/react-19-61DAFB?logo=react&logoColor=black)](https://react.dev)

</div>

---

SkillHub 是一个可私有化部署的平台，为团队提供内部共享 Agent 技能的空间。上传技能包、推送到命名空间，其他人通过搜索或 CLI 即可发现和安装。专为防火墙后的本地部署设计，具备公共注册中心的完整功能体验。

## 核心特性

- **私有化部署** — 在自己的基础设施上运行，将专有技能保持在防火墙内，数据完全自主可控。一条 `make dev-all` 命令即可本地启动。
- **发布与版本管理** — 上传 Agent 技能包，支持语义化版本号、自定义标签（`beta`、`stable`）和自动 `latest` 追踪。
- **技能发现** — 全文搜索，支持按命名空间、下载量、评分和时间筛选。可见性规则确保用户只能看到被授权的内容。
- **团队命名空间** — 在团队或全局范围下组织技能，每个命名空间拥有独立的成员、角色（Owner / Admin / Member）和发布策略。
- **审核与治理** — 团队管理员在命名空间内审核，平台管理员控制向全局范围的推广。治理操作均有审计日志记录。
- **社交功能** — 收藏、评分和下载追踪，围绕组织的最佳实践构建社区。
- **账户合并** — 将多个 OAuth 身份和 API Token 整合到单一用户账户下。
- **API Token 管理** — 为 CLI 和程序化访问生成带范围的 Token，使用前缀安全哈希。
- **CLI 优先** — 原生 REST API 加上 ClawHub 兼容层。原生 CLI API 是主要支持路径，协议兼容性持续扩展。
- **可插拔存储** — 开发环境使用本地文件系统，生产环境使用 S3 / MinIO，通过配置切换。
- **国际化** — 基于 i18next 的多语言支持。

## 快速开始

使用以下命令启动完整的本地环境：

```bash
rm -rf /tmp/skillhub-runtime
curl -fsSL https://imageless.oss-cn-beijing.aliyuncs.com/runtime.sh | sh -s -- up
```

默认拉取 `latest` 稳定版镜像。使用 `--version edge` 获取 `main` 分支的最新构建。

**配置公网访问 URL（生产环境推荐）：**

```bash
curl -fsSL https://imageless.oss-cn-beijing.aliyuncs.com/runtime.sh | sh -s -- up --public-url https://skillhub.your-company.com
```

`--public-url` 参数设置 SkillHub 实例的公网访问地址，确保：
- CLI 安装命令显示正确的注册中心 URL
- Agent 配置说明显示正确的 skill.md URL
- OAuth 回调和设备认证链接正常工作

**国内用户（阿里云镜像）：**

```bash
curl -fsSL https://imageless.oss-cn-beijing.aliyuncs.com/runtime.sh | sh -s -- up --aliyun --public-url https://skillhub.your-company.com
```

如果部署遇到问题，清除现有的运行时目录后重试。

### 环境要求

- Docker & Docker Compose

### 本地开发

```bash
make dev-all
```

启动后访问：

- Web 界面：`http://localhost:3000`
- 后端 API：`http://localhost:8080`

默认情况下，`make dev-all` 使用 `local` 配置启动后端。此模式下会创建以下模拟用户和引导管理员账户：

- `local-user` — 普通用户，用于发布和命名空间操作
- `local-admin` — 拥有 `SUPER_ADMIN` 权限，用于审核和管理操作

本地引导管理员默认启用（配置在 `application-local.yml`）：

- 用户名：`admin`
- 密码：`ChangeMe!2026`
- 禁用方式：启动后端前设置 `BOOTSTRAP_ADMIN_ENABLED=false`

停止所有服务：

```bash
make dev-all-down
```

重置本地依赖并从干净状态启动：

```bash
make dev-all-reset
```

运行 `make help` 查看所有可用命令。

常用后端命令：

```bash
make test
make test-backend-app
make build-backend-app
```

> 不要直接在 `server/` 下运行 `./mvnw -pl skillhub-app clean test`。`skillhub-app` 依赖同级模块，独立构建可能使用本地 Maven 仓库中的过时构件，导致误导性的编译错误。请使用 `-am` 参数或上述 `make` 命令。

### 容器运行时

运行时镜像由 GitHub Actions 构建并推送到 GHCR，支持 `linux/amd64` 和 `linux/arm64` 架构。

**一键部署：**

```bash
# 默认（GHCR 镜像）
curl -fsSL https://imageless.oss-cn-beijing.aliyuncs.com/runtime.sh | sh -s -- up --public-url https://skillhub.your-company.com

# 阿里云镜像（国内推荐）
curl -fsSL https://imageless.oss-cn-beijing.aliyuncs.com/runtime.sh | sh -s -- up --aliyun --public-url https://skillhub.your-company.com
```

**部署参数：**

| 参数 | 说明 | 示例 |
|------|------|------|
| `--public-url <url>` | 公网访问 URL（推荐） | `--public-url https://skill.example.com` |
| `--version <tag>` | 指定镜像版本 | `--version v0.2.0` |
| `--aliyun` | 使用阿里云镜像 | `--aliyun` |
| `--home <dir>` | 运行时目录 | `--home /opt/skillhub` |
| `--no-scanner` | 禁用安全扫描 | `--no-scanner` |

> **重要**：生产环境请配置 `--public-url`，确保 CLI 安装命令和 Agent 配置说明显示正确的 URL。

**手动部署：**

```bash
cp .env.release.example .env.release
# 编辑 .env.release，设置 SKILLHUB_VERSION 和 SKILLHUB_PUBLIC_BASE_URL
make validate-release-config
docker compose --env-file .env.release -f compose.release.yml up -d
```

生产环境建议：

- 设置 `SKILLHUB_PUBLIC_BASE_URL` 为最终的 HTTPS 入口地址
- 将 PostgreSQL / Redis 绑定到 `127.0.0.1`
- 使用外部 S3 / OSS（通过 `SKILLHUB_STORAGE_S3_*` 配置）
- 修改 `BOOTSTRAP_ADMIN_PASSWORD` 为强密码
- 初始设置完成后轮换或禁用引导管理员
- 启动前运行 `make validate-release-config`

## 架构

```
┌─────────────┐     ┌─────────────┐     ┌──────────────┐
│   Web 界面   │     │  CLI 工具    │     │  REST API    │
│  (React 19) │     │             │     │              │
└──────┬──────┘     └──────┬──────┘     └──────┬───────┘
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
                    ┌──────▼──────┐
                    │   Nginx     │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │ Spring Boot │  认证 · RBAC · 核心服务
                    │   (Java 21) │  OAuth2 · API Token · 审计
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
       ┌──────▼───┐  ┌─────▼────┐  ┌────▼────┐
       │PostgreSQL│  │  Redis   │  │  存储    │
       │    16    │  │    7     │  │ S3/MinIO│
       └──────────┘  └──────────┘  └─────────┘
```

**后端（Spring Boot 3.2.3, Java 21）：**
- 多模块 Maven 项目，采用整洁架构
- 模块：app、domain、auth、search、storage、infra
- PostgreSQL 16 + Flyway 数据库迁移
- Redis 会话管理
- S3 / MinIO 技能包存储

**前端（React 19, TypeScript, Vite）：**
- TanStack Router 路由
- TanStack Query 数据请求
- Tailwind CSS + Radix UI 样式
- OpenAPI TypeScript 类型安全的 API 客户端
- i18next 国际化

## Agent 平台集成

SkillHub 可作为多个 Agent 平台的技能注册中心后端。将以下客户端指向你的 SkillHub 实例，即可发布、发现和安装技能。

### ClawHub CLI

```bash
# 配置注册中心 URL
export CLAWHUB_REGISTRY=https://skillhub.your-company.com

# 认证
clawhub login --token YOUR_API_TOKEN

# 搜索和安装技能
clawhub search email
clawhub install my-skill
clawhub install my-namespace--my-skill

# 发布技能
clawhub publish ./my-skill
```

> 提示：通过指定安装目录（`--dir`），可将技能安装到其他 CLI Coding Agent。例如：`clawhub --dir ~/.claude/skills install my-skill`

## 技术栈

| 组件 | 技术 |
|------|------|
| 后端框架 | Spring Boot 3.2.3 (Java 21) |
| 前端框架 | React 19 + TypeScript + Vite |
| 数据库 | PostgreSQL 16 |
| 缓存/会话 | Redis 7 |
| 对象存储 | S3 / MinIO / 本地文件系统 |
| 反向代理 | Nginx |
| 容器化 | Docker + Docker Compose |

## 贡献

欢迎贡献！请先开 Issue 讨论你想要做的改动。

- 贡献指南：[`CONTRIBUTING.md`](./CONTRIBUTING.md)
- 行为准则：[`CODE_OF_CONDUCT.md`](./CODE_OF_CONDUCT.md)

## 许可证

Apache License 2.0
