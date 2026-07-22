# docker-stacks

集中管理各类服务的 Docker Compose 编排文件。所有容器统一用 `docker compose` 编排，不再用 `docker run` 命令行手动创建。

## 目录结构

每个服务独立一个子目录，包含自己的 `compose.yaml` 和 `.env.example`：

```
docker-stacks/
├── mongodb/     # MongoDB + mongo-express 管理界面
└── jenkins/     # Jenkins CI
```

## 通用用法

```bash
cd <服务目录>
cp .env.example .env   # 首次使用，按需修改端口/密码
docker compose up -d   # 启动
docker compose ps      # 查看状态
docker compose logs -f # 查看日志
docker compose down    # 停止（数据卷保留）
```

> `.env` 含密码等敏感信息，已通过 `.gitignore` 排除，不会提交。克隆仓库后需从 `.env.example` 复制生成。

## 服务列表

### mongodb

MongoDB 7 + mongo-express 管理界面。

| 项 | 说明 |
|---|---|
| MongoDB 端口 | `27017`（`MONGO_PORT`） |
| 管理界面端口 | `8081`（`ME_PORT`） |
| 数据持久化 | 命名卷 `mongodb_data` / `mongodb_config` |
| 认证 | 已开启，root 账号见 `.env` |

- 连接串：`mongodb://<user>:<pass>@localhost:27017/?authSource=admin`
- 管理界面：http://localhost:8081 （Basic Auth，账号见 `.env`）

### jenkins

Jenkins LTS，由原 `docker run` 迁移而来。

| 项 | 说明 |
|---|---|
| Web 端口 | `8082`（`JENKINS_WEB_PORT`）→ 容器 8080 |
| Agent 端口 | `50000`（`JENKINS_AGENT_PORT`） |
| 数据持久化 | **外部**命名卷 `jenkins_home`（复用迁移前的卷） |

- 访问：http://localhost:8082
- `jenkins_home` 为 external 卷，需已存在；全新部署时先执行 `docker volume create jenkins_home`。
