# AGENTS.md

本仓库集中管理各类服务的 Docker Compose 编排文件。面向 AI 编码助手的项目约定如下。

## 核心原则

- **只用 `docker compose` 编排容器，不用 `docker run` 命令行手动创建。** 新增服务一律写成 compose 文件。
- 修改保持最小化，只动与需求直接相关的内容。

## 目录约定

- 每个服务独立一个子目录，目录名即服务名（如 `mongodb/`、`jenkins/`）。
- 每个服务目录至少包含：
  - `compose.yaml` —— 编排定义（用 `compose.yaml`，不用旧的 `docker-compose.yml`）。
  - `.env.example` —— 环境变量模板，可安全提交。

## 密钥与环境变量

- **敏感信息（密码、令牌等）一律走 `.env`，绝不写死在 `compose.yaml` 里。**
- `.env` 已被 `.gitignore` 排除，**禁止提交**；只提交 `.env.example` 模板。
- compose 中引用变量时带默认值兜底，如 `${MONGO_PORT:-27017}`。
- 新增任何服务，务必同步更新对应的 `.env.example`。

## 数据持久化

- 用命名卷（named volume）持久化数据，不用匿名卷。
- 从既有 `docker run` 容器迁移时，若卷已存在，用 `external: true` 复用，避免丢数据。

## 常规约定

- 生产/长期服务设 `restart: unless-stopped`。
- 有条件时加 `healthcheck`；依赖其他服务的用 `depends_on` + `condition: service_healthy`。
- 新增服务后，更新 `README.md` 的「服务列表」。

## 提交规范

- 遵循 Conventional Commits（`feat:` / `fix:` / `docs:` / `chore:` 等），提交信息用中文描述。
