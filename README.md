# Mia AI Office

AI 專員辦公室。靜態網頁由 GitHub Pages 提供。

網站：<https://miachang25.github.io/mia-ai-office/>

`index.html` 是辦公室畫面。右上角可以切換日常、工作任務、咖啡休息。

## status.json

檔案放在儲存庫根目錄。頁面開啟時會讀取一次，之後每 60 秒再讀一次（網址加上當下時間，避免瀏覽器沿用舊內容）。

讀取失敗時（例如直接用檔案開啟，或檔案暫時不在）頁面維持原本內建的活動，不另外跳錯誤。

改完後推上 `main`，Actions 會重新發布。發布完成後，已打開的頁面最慢約一分鐘會吃到新內容。請一併更新 `updated`。

### 欄位

| 欄位 | 說明 |
| --- | --- |
| `updated` | ISO 8601 時間，例如 `2026-10-27T00:00:00Z`。頁面左上角顯示「最後更新」（台北時間）。 |
| `mode` | 整體模式：`daily` 日常、`work` 工作任務、`break` 咖啡休息。`null` 表示不強制切換，沿用畫面上的模式。 |
| `taskFormUrl` | 「交辦任務」要開的網址，必須是 `http` 或 `https`。空字串時按鈕隱藏。 |
| `agents` | 各專員的狀態。鍵名要和畫面上的名字一樣。 |

`mode` 為 `null`、而且每個人都是 `idle` 時，辦公室維持原本的內建活動。有人進入 `working` 或 `review`，或 `mode` 被指定時，才改由這份檔案主導。

### 每位專員

| 欄位 | 說明 |
| --- | --- |
| `status` | `idle` 待命，跟著目前模式活動；`working` 坐在自己的位子上工作；`review` 坐在位子上審核。 |
| `task` | 頭上泡泡的文字。`working`、`review` 而這裡是空的時，分別顯示「💻 工作中」、「🔍 審核中」。待命時不顯示這段文字。 |

名字：`小真`、`點點`、`小票兒`、`樂樂`、`暖暖`。泡泡是一行短句即可。

```json
{
  "updated": "2026-10-27T01:30:00Z",
  "mode": "work",
  "taskFormUrl": "https://example.com/task-form",
  "agents": {
    "小真": { "status": "review", "task": "審核今日成果" },
    "點點": { "status": "working", "task": "整理今日待辦" },
    "小票兒": { "status": "working", "task": "詢價中" },
    "樂樂": { "status": "idle", "task": "" },
    "暖暖": { "status": "idle", "task": "" }
  }
}
```

畫面上的模式按鈕仍可暫時切換場景。若 `mode` 不是 `null`，下次成功讀到 `status.json` 時會改回檔案裡的模式。

## 發布

推上 `main` 會跑 `.github/workflows/pages.yml`（`configure-pages` 的 `enablement: true`、`upload-pages-artifact`、`deploy-pages`）。

預設的 `GITHUB_TOKEN` 不能替儲存庫建立 Pages 網站。若 Actions 在 Configure Pages 失敗（`Create Pages site failed` 或 `Get Pages site failed`），請手動開一次：

1. 打開此儲存庫的 **Settings**
2. 左側點 **Pages**
3. **Build and deployment** → **Source** 選 **GitHub Actions**

接著到 **Actions**，打開失敗的 **Deploy GitHub Pages**，按 **Re-run jobs**。Source 已經是 GitHub Actions 之後，每次推上 `main` 都會自動更新。
