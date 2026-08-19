# Google Sheet + Google Apps Script 後台設定指南

這份專案採用：

- **GitHub Pages**：前台網站
- **Google Sheet**：資料庫
- **Google Apps Script**：前台與 Sheet 之間的 API
- **Google Drive**：PDF、LOI、締結文件、租借契約等正式文件

## 一、建立 Google Sheet

1. 新建一份 Google Sheet，例如：`阿里山林鐵_國際合作資料庫`。
2. 進入「擴充功能 → Apps Script」。
3. 將 `backend/apps-script/Code.gs` 全部貼入 Apps Script 的 `Code.gs`。
4. Apps Script 左側「專案設定」將時區設為 `Asia/Taipei`。
5. 在 Apps Script 上方函式下拉選單選 `setupDatabase`，按「執行」。
6. 第一次會要求 Google 授權，依畫面完成授權。
7. 回到 Sheet，會自動出現：
   - `Railways`
   - `Records`
   - `Editors`
   - `Documents`

### 4 張 Sheet 的用途

#### Railways
鐵路基本資料。鐵路新增、改名、停止顯示，都從這張表維護。

#### Records
每一筆國際交流紀錄。最核心的 4 個必要資訊：
- railway_id
- date
- type
- title

其他摘要、標籤、關聯紀錄、合作串、編輯人都可後補。

#### Editors
編輯人下拉名單。
- `active = TRUE`：新增紀錄時出現
- `active = FALSE`：不再出現在新紀錄選單，但舊資料仍保留姓名

#### Documents
正式合作文件索引。PDF 本身建議放 Google Drive，Sheet 只存連結。

---

## 二、建立 Google Drive 文件資料夾

建議結構：

```
阿里山林鐵_國際合作文件
├─ 日本
│  ├─ 大井川鐵道
│  └─ 黑部峽谷鐵道
├─ 印度
├─ 瑞士
├─ 英國
│  └─ 威爾普蘭菲爾鐵路
│     ├─ 締結與續約
│     └─ DL-34
└─ 澳洲
```

PDF 上傳 Drive 後，把「檢視連結」貼進 `Documents.file_url`。

注意：網站是否能開 PDF，取決於該 Drive 檔案的分享權限。如果只讓團隊內部 Google 帳號觀看，就把 Drive 權限維持在組織／指定人員即可；不用為了網站而設成公開。

---

## 三、建立編輯人名單

在 `Editors` 填：

| editor_id | name | active | note |
|---|---|---|---|
| editor-001 | Ida | TRUE | |
| editor-002 | 王○○ | TRUE | |
| editor-003 | 陳○○ | FALSE | 已離開專案 |

不建議刪除舊同事，只改成 FALSE。

---

## 四、設定「不用登入」的簡單編輯碼（建議）

如果 GitHub Pages 是公開網址，而 Apps Script 允許任何人送出資料，理論上知道 API 網址的人都可能新增資料。

不想做 Google 登入，可以使用共同編輯碼：

1. Apps Script → 左側「專案設定」。
2. 找「指令碼屬性」。
3. 新增：
   - 屬性：`WRITE_KEY`
   - 值：自行設定，例如一組只有專案同事知道的字串。
4. 前台新增／修改資料時，再輸入這組編輯碼。

**不要把編輯碼直接寫死在公開 GitHub 的 JavaScript 裡。**

這不是企業級帳號權限系統，但比完全匿名寫入安全很多，而且沒有登入流程。

---

## 五、部署 Apps Script 為 Web App

1. Apps Script 右上角「部署 → 新部署」。
2. 類型選「網路應用程式」。
3. 執行身分建議選「我」。
4. 可存取對象依你帳號政策選擇；若 GitHub Pages 要直接呼叫，通常需要允許網站使用者可以存取該 Web App。
5. 部署後取得一個 `/exec` 網址。
6. 保存這個網址，之後貼到前台設定裡。

Apps Script Web App 需要 `doGet(e)` 或 `doPost(e)`；本專案的 `Code.gs` 已經準備好。

---

## 六、API 測試

部署完成後，把網址貼進瀏覽器：

```
YOUR_WEB_APP_URL?action=bootstrap
```

成功時會看到 JSON，包含：
- railways
- records
- editors
- documents

也可以測：

```
YOUR_WEB_APP_URL?action=editors
```

---

## 七、下一步：把 Prototype 真正接上 Sheet

目前 `index.html` 還是 Prototype 內建資料，方便你先確認 UI。

正式串接時要做兩件事：

1. 網頁開啟時，用 `fetch(WEB_APP_URL + '?action=bootstrap')` 讀取 Google Sheet 資料。
2. 新增紀錄時，用 POST 將資料送到 `createRecord`。

為避免跨來源請求的預檢問題，範例後端支援將 JSON 放在一般表單欄位 `payload` 送出。

POST payload 範例：

```json
{
  "action": "createRecord",
  "writeKey": "由同事操作時輸入，不要寫死在 GitHub",
  "data": {
    "railway_id": "wllr",
    "date": "2026-08-19",
    "type": "議題交流",
    "title": "DL-34 測試進度更新",
    "summary": "英方回覆測試進度。",
    "tags": ["DL-34", "維修"],
    "parent_record_id": "rec-xxx",
    "thread_id": "thread-dl34",
    "editor": "Ida"
  }
}
```

---

## 八、建議先後順序

最省力的導入順序：

1. 先把 GitHub Prototype 上線。
2. 建立 Google Sheet。
3. 執行 `setupDatabase()`。
4. 先維護 `Editors` 與 `Documents`。
5. 把現有 Word 年表逐步搬到 `Records`。
6. 最後再讓 GitHub 前台從 Sheet 即時讀寫。
7. 待資料穩定後，再做「匯出 Word 正式年表」。

不建議一開始就把所有 32 頁 Word 一次搬完。先用目前最常更新的英國、澳洲、瑞士測試一輪，確認欄位夠用，再大量匯入。

---

## v3：公開瀏覽＋Google Email 白名單編輯

此版本已將原本共用編輯碼改為 Google Email 白名單架構。請另外閱讀 `AUTH-AND-VISIBILITY.md`。

資料表欄位也已更新：
- Editors：增加 `email`
- Records：改由後端自動寫入 `editor_email`
- Documents：增加 `visibility`，可選 public / internal / restricted

真正上線 Google Sign-In 前，還需要建立 Google Identity Services 的 Web Client ID，並將同一 Client ID 設入 Apps Script 指令碼屬性 `GOOGLE_CLIENT_ID`。
