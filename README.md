# ARTEX 部署（Docker Compose）

内网服务器部署记录：`ruoji6/artex:latest`（v0.3.30）+ `postgres:16-alpine`。

## 目录说明

| 路径 | 说明 |
| --- | --- |
| `docker-compose.yml` | 服务编排（artex + postgres） |
| `.env.example` | 配置模板，复制为 `.env` 使用 |
| `skills/` | 挂载到容器 `/app/skills` |
| `data/` | 运行时数据（jwt.key、sqlite），**未纳入版本库** |
| `config.example.json` | 单二进制运行时的配置样例 |

## 部署步骤

```bash
cp .env.example .env      # 填 POSTGRES_PASSWORD、OPENAI_API_KEY 等
docker compose up -d
```

访问 `http://<服务器IP>:8787`，首次进 `/setup` 设置管理员密码。

## 服务

- `artex-artex-1`：应用，监听 8787；8788 为流量录制代理，仅绑定 127.0.0.1
- `artex-postgres-1`：PostgreSQL 16，数据卷 `artex_pgdata`
- 健康检查：`GET /api/health` → `{"ok":true}`

## 常用命令

```bash
docker compose ps
docker compose logs -f artex
docker compose down
docker compose pull && docker compose up -d artex   # 更新镜像
```

## 注意

- `POSTGRES_PASSWORD` 不要含 `#` `!` 等特殊字符：它会被拼进 DSN，未编码会导致启动失败。
- `data/` 与 `.env` 含敏感信息，已加入 `.gitignore`，请勿提交。

## 备份与报告样例

- `secure/` — 加密备份（运行时配置与数据、扫描任务产物分片），解密方法见 `secure/README.md`。
- `reports/report_redacted.png` — 渗透测试报告脱敏样例图（系统名 / IP / 口令均已占位）。
