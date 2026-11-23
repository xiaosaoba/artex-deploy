# 加密备份

`artex_backup.tar.gz.enc`：AES-256-CBC（PBKDF2 200000 次迭代）加密的部署快照。

包含：`.env`、`artex_db.sql`（pg_dump 逻辑备份）、`data/sessions`、`data/traffic`、`data/transcripts`。
不含 414MB 的 `data/tasks`（扫描证据）。

## 解密

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -iter 200000 \
  -in artex_backup.tar.gz.enc -out artex_backup.tar.gz \
  -pass pass:'<你的口令>'
tar -xzf artex_backup.tar.gz
```

## 恢复数据库

```bash
docker exec -i artex-postgres-1 psql -U artex -d artex < artex_db.sql
```

口令单独保管，不要提交到仓库。
