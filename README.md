# 設計協作平台

[Penpot](https://penpot.app) 開源設計工具本地部署設定，介面繁體中文。

**Port:** 9001 | **URL:** http://localhost:9001

## 快速啟動

```bash
cd ~/penpot
docker compose up -d
```

## 容器架構

| 容器 | 說明 |
|------|------|
| `penpot-frontend` | nginx，port 9001 |
| `penpot-backend` | API server |
| `penpot-exporter` | PDF / PNG 匯出 |
| `penpot-postgres` | 主資料庫 |
| `penpot-redis` | 快取 / 即時通知 |

## 中文化

- **介面標題**：`index.html` bind mount，標題改為「設計協作平台」
- **使用者語言**：PostgreSQL `profile.lang = 'zh-Hant'`

## 相關 Repo

| Repo | 說明 |
|------|------|
| [gitea](https://github.com/comtnet86city-debug/gitea) | 設計協作倉庫 |
| [claude-workspace](https://github.com/comtnet86city-debug/claude-workspace) | 完整工作區文件 |
