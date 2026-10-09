# AI Fitness Coach — 開發協作規範

多人開發的 AIoT 專題。本文件只規範倉庫、分支、合併與日常協作，不包含產品設計或畫面規格。

- 主倉：<https://github.com/115NCHUAIOT/ai-fitness-coach.git>
- 筆記：<https://hackmd.io/Eep5KbKmRgyBH-RWIys7Pg>

## 倉庫

| 倉庫 | 用途 | 連結 |
| --- | --- | --- |
| ai-fitness-coach | 運動教練主專案 | <https://github.com/115NCHUAIOT/ai-fitness-coach.git> |
| CyborAssistant | 數位人像 | <https://github.com/115NCHUAIOT/CyborAssistant> |
| CyborAgent | 對話框 | <https://github.com/115NCHUAIOT/CyborAgent> |

跨倉改動先在對應倉庫開分支與 PR，不要把人像或對話程式直接塞進主倉。

## 分支

- `main`：可展示版本。不直接 push。
- `dev`：日常整合。功能完成後先合進這裡。
- 功能分支從 `dev` 拉出，完成後 PR 回 `dev`。
- `dev` 穩定後再 PR 到 `main`。

既有功能分支：

| 分支 | 範圍 | 負責人 |
| --- | --- | --- |
| `GAS` | 天氣、定位、地圖路線 | 怡禎 |
| `calories` | 拍照、Agent、食物熱量 | 允柔 |
| `MCP` | 營養菜單、資料庫 / MCP | 順陞 |
| `icon` | AI 人像、即時說話與動作、對話框 | Shaun |

命名：

```text
<type>/<short-name>

feat/weather-route
fix/step-counter-null
chore/env-example
```

`type` 用 `feat`、`fix`、`chore`、`docs`、`refactor`。名稱用英文小寫與連字號。

## 模組負責人

負責人對該模組的分支、介面與 PR 有決定權。改到別人的模組要先在 PR 或群組說明。

| 模組 | 負責人 |
| --- | --- |
| 計步、動作感測 | unknown |
| 天氣、定位、地圖路線 | 怡禎 |
| 拍照、食物熱量 | 允柔 |
| 營養菜單、資料庫 / MCP | 順陞 |
| AI 人像、對話框 | Shaun |

## 日常流程

```bash
git checkout dev
git pull origin dev
git checkout -b feat/your-topic

# 開發、提交
git push -u origin feat/your-topic
# 開 PR：feat/your-topic -> dev
```

合併後刪除遠端功能分支。下次開發再從最新 `dev` 開新分支。

## Commit

格式：

```text
<type>: <做了什麼>

feat: add CWA rainfall lookup
fix: handle empty step count
chore: ignore local sqlite
```

一筆 commit 只做一件事。不要把格式化、功能與無關檔案混在同一筆。

## Pull Request

- 目標分支預設 `dev`。
- 標題與 commit 相同格式。
- 內文寫：改了什麼、怎麼驗、是否要新的環境變數。
- 至少 1 人 review。碰到對方模組時，要該模組負責人 review。
- 有衝突由 PR 作者 rebase 或 merge `dev` 後自行解。
- 未通過不自行合入 `main`。

## 環境與機密

- 密鑰只放本機 `.env`，由 `.env.example` 列出鍵名，不填真值。
- `.env`、資料庫檔、憑證不得 commit。
- 新增環境變數時，同步改 `.env.example`，並在 PR 說明用途。
- 技術棧：Python、FastAPI、Vanilla JavaScript / TypeScript、SQLite、Cloudflare D1、MCP。

本地起步：

```bash
git clone https://github.com/115NCHUAIOT/ai-fitness-coach.git
cd ai-fitness-coach
git checkout dev

python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS / Linux
source .venv/bin/activate

pip install -r requirements.txt
cp .env.example .env
```

## 不要提交

- `.env`、API 金鑰、憑證
- `node_modules/`、`.venv/`、`__pycache__/`
- 本地 SQLite、上傳的照片、大型原始影片
- 編輯器個人設定（`.vscode/` 除非團隊約定要共享）

## 溝通

- 介面、分支策略、合併問題寫在 PR 或 issue。
- 只改自己的分支；不要 force-push 別人的分支或 `dev` / `main`。
- 共用契約（API 路徑、回傳欄位、資料表）變更前先跟呼叫方講一聲。