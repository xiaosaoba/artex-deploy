# secure/ — 加密备份说明

本目录所有文件均为 **AES-256-CBC（PBKDF2，200000 次迭代，带 salt）** 加密产物。
解密口令**不随仓库分发**，单独保管。

## 1. 运行时配置与数据备份（artex_backup.tar.gz.enc）

内容：真实 `.env`、PostgreSQL 逻辑备份（pg_dump）、`data/sessions`、`data/traffic`、`data/transcripts`。

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -iter 200000 \
  -in artex_backup.tar.gz.enc -out artex_backup.tar.gz
tar -xzf artex_backup.tar.gz
```

## 2. 扫描任务产物（artex_tasks.part_00 ~ part_03）

内容：`data/tasks`（多任务扫描证据，含靶场 jar、dump、日志等），共约 281 MB，按 90 MB 切分以绕过 GitHub 单文件 100 MB 限制。

```bash
# 合并分片（顺序不可变）
cat artex_tasks.part_00 artex_tasks.part_01 artex_tasks.part_02 artex_tasks.part_03 \
  > artex_tasks.tar.gz.enc

# 校验合并结果（应为 295dc610a3e911a302d300e7b50d671ecc4385dc021ef3c935a64f5fcd29b20e）
sha256sum artex_tasks.tar.gz.enc
sha256sum -c artex_tasks.parts.sha256   # 校验各分片

# 解密解包
openssl enc -d -aes-256-cbc -pbkdf2 -iter 200000 \
  -in artex_tasks.tar.gz.enc -out artex_tasks.tar.gz
tar -xzf artex_tasks.tar.gz
```

## 3. 报告脱敏样例（reports/）

`reports/report_redacted.png`：ARTEX 渗透测试报告的**脱敏**样例图，系统名、IP、端口、口令、云凭据均已占位替换，仅用于展示报告形态。
`reports/report_redacted.html`：该图的 HTML 源文件，可按需再渲染。

## 安全提醒

- 加密文件与口令必须分开保存；仓库为私有，但仍不建议长期保留敏感数据的副本。
- 恢复后请立即重设 `.env` 中的数据库口令与 LLM API Key。
