# openclaw-docker

使用 Docker Compose 本地运行 OpenClaw 网关的最小示例。

## 环境要求

- Docker Desktop（macOS/Windows）或 Docker Engine（Linux）
- Docker Compose v2

## 快速开始

1) 配置环境变量

```bash
cp .env .env.local 2>/dev/null || true
# 直接编辑 .env，至少确认以下变量：
# - OPENCLAW_GATEWAY_TOKEN
# - OPENCLAW_GATEWAY_PORT
```

2) 启动网关

```bash
docker compose pull
docker compose up -d openclaw-cn-gateway
```

3) 访问控制台

- 默认地址：`http://127.0.0.1:18789/`
- 建议使用带 token 的地址登录：

```bash
docker compose run --rm openclaw-cn-cli dashboard --no-open
```

## 常用命令

启动/重启：

```bash
docker compose up -d openclaw-cn-gateway
docker compose up -d --force-recreate openclaw-cn-gateway
```

查看状态与日志：

```bash
docker compose ps
docker compose logs -f openclaw-cn-gateway
```

停止：

```bash
docker compose stop openclaw-cn-gateway
docker compose down
```

## 常见问题

### 1) `gateway token mismatch`

原因：浏览器里保存的 token 与网关 token 不一致。  
处理：

- 确认 `.env` 的 `OPENCLAW_GATEWAY_TOKEN` 已设置
- 重建网关：`docker compose up -d --force-recreate openclaw-cn-gateway`
- 使用 tokenized URL 重新进入
- 必要时清浏览器站点缓存/localStorage

### 2) `pairing required`

原因：当前设备未通过配对审批。  
处理（在网关容器内查看并批准）：

```bash
docker compose exec -T openclaw-cn-gateway node dist/index.js devices list
docker compose exec -T openclaw-cn-gateway node dist/index.js devices approve <requestId>
```

### 3) 模型 404 / `model not_found`

原因：配置的模型 ID 不在当前 API Key 可用列表。  
处理思路：

- 使用可用模型 ID（例如当前已验证可用：`claude-sonnet-4-6`）
- `anthropic-messages` 场景建议 `baseUrl` 使用：`https://api.anthropic.com`

## 目录说明

- `docker-compose.yml`：服务定义
- `.env`：本地环境变量（已在 `.gitignore` 忽略）
- `data/.openclaw`：本地状态与配置
- `data/clawd`：工作区与运行产物

## 安全建议

- 不要提交 `.env`、密钥或会话信息
- 定期轮换 API Key 与网关 token
