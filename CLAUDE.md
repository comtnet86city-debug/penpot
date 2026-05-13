# 設計協作平台（Penpot）

開源設計工具，Figma 替代方案，以 Docker Compose 本地部署。

**GitHub:** `comtnet86city-debug/penpot`
**Port:** 9001 | **URL:** http://localhost:9001
**介面語言:** 繁體中文（zh-Hant）

## 啟動 / 停止

```bash
cd ~/penpot
docker compose up -d      # 背景啟動
docker compose down       # 停止
docker compose ps         # 查看狀態
docker compose logs -f    # 查看日誌
```

或直接執行 `start-all`（含所有服務）。

## 檔案結構

```
penpot/
├── docker-compose.yml   # 服務定義
├── index.html           # 修改版前端首頁（繁中標題，bind mount 覆蓋容器）
└── .gitignore
```

## 容器清單

| 容器 | Image | 說明 |
|------|-------|------|
| `penpot-frontend` | `penpotapp/frontend:latest` | nginx，port 9001→8080 |
| `penpot-backend` | `penpotapp/backend:latest` | API server，port 6060 |
| `penpot-exporter` | `penpotapp/exporter:latest` | 匯出 PDF/PNG，port 6061 |
| `penpot-postgres` | `postgres:15` | 主資料庫 |
| `penpot-redis` | `redis:7` | 快取 / 即時通知 |

## 中文化設定

### 介面標題（index.html bind mount）
`index.html` 複製自容器 `/var/www/app/index.html`，修改後以唯讀 bind mount 覆蓋：
```yaml
volumes:
  - ./index.html:/var/www/app/index.html:ro
```
修改內容：
- `<title>` → `設計協作平台`
- OG / Twitter title → `設計協作平台`
- description → `設計與原型製作的開源解決方案。`

**升級映像後須重新確認 index.html 是否需更新：**
```bash
docker cp penpot-frontend:/var/www/app/index.html /tmp/index_new.html
diff /tmp/index_new.html ~/penpot/index.html
```

### 使用者介面語言（PostgreSQL）
使用者語言存於資料庫 `profile.lang` 欄位，已設為 `zh-Hant`：
```bash
docker exec penpot-postgres psql -U penpot -d penpot \
  -c "SELECT email, lang FROM profile WHERE deleted_at IS NULL;"
# 新增使用者後執行：
docker exec penpot-postgres psql -U penpot -d penpot \
  -c "UPDATE profile SET lang='zh-Hant' WHERE lang IS NULL OR lang='';"
```

> Penpot 無全局強制語言設定，新使用者需自行到個人設定選擇繁體中文。

## 重要環境變數

| 變數 | 值 | 說明 |
|------|-----|------|
| `PENPOT_FLAGS` | `enable-login-with-password enable-registration` | 開放帳號自助註冊 |
| `PENPOT_SECRET_KEY` | `penpot-secret-key-change-in-production` | **正式環境請更換** |
| `PENPOT_PUBLIC_URI` | `http://localhost:9001` | 外部存取 URL |
| `PENPOT_TELEMETRY_ENABLED` | `false` | 停用遙測 |

## Docker Volume

| Volume | 用途 |
|--------|------|
| `penpot_assets` | 上傳的設計資產（字型、圖片） |
| `penpot_postgres` | PostgreSQL 資料庫資料 |

查看 volume 路徑：`docker volume inspect penpot_penpot_assets`

## 首次使用

1. 開瀏覽器進入 http://localhost:9001
2. 點「建立帳號」自助註冊
3. 登入後到個人設定 → 語言 → 選「中文（繁體）」

## 參見

完整工作區環境說明（LLM 模型、外部服務、OpenClaw、所有 repo 清單）：
`comtnet86city-debug/claude-workspace` → `~/CLAUDE.md`
