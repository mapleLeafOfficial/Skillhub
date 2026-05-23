# 贡献指南

## 范围

SkillHub 是一个可私有化部署的 Agent 技能注册中心。贡献内容应保持现有架构和 [`docs/`](./docs) 中记录的产品方向一致。

## 开始之前

- 阅读 [`README.md`](./README.md) 了解本地开发命令。
- 修改行为前先查阅相关设计文档。
- 非简单的改动请先开 Issue 讨论，再提交大型 Pull Request。

## 开发环境

前置要求：

- Docker 和 Docker Compose
- Java 21
- Node.js 和 `pnpm`

启动本地环境：

```bash
make dev-all
```

常用命令：

```bash
make test
make typecheck-web
make build-web
make generate-api
./scripts/check-openapi-generated.sh
./scripts/smoke-test.sh
```

停止服务：

```bash
make dev-all-down
```

## 变更规范

- 保持改动聚焦，避免将重构和行为变更混在一起。
- 遵循 `server/`、`web/` 和 `docs/` 中现有的模块边界。
- 行为变更时添加或更新测试。
- API、认证流程、部署或运维流程变更时更新文档。
- 后端 OpenAPI 契约变更时，运行 `make generate-api` 并提交 `web/src/api/generated/schema.d.ts`。
- 优先保持向后兼容，除非 Issue 明确允许破坏性变更。

## Pull Request

提交 Pull Request 前请确保：

- 分支已与目标分支干净合并或变基。
- 相关后端测试通过。
- 前端文件变更时类型检查和构建通过。
- 后端 API 契约变更时已运行 `make generate-api` 或 `./scripts/check-openapi-generated.sh`。
- 运维相关流程变更时已更新冒烟测试。
- PR 描述说明动机、范围和发布影响。

## 提交风格

推荐使用约定式提交格式，例如：

- `feat(auth): add local account login`
- `fix(ops): align smoke test with csrf flow`
- `docs(deploy): clarify runtime image usage`

## 安全问题报告

请勿公开提交安全漏洞相关 Issue。

使用 GitHub Security Advisories 或内部安全流程私下向维护者报告。
