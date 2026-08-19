# 權限與文件可見性設定

## 1. 整體原則

- 網站：公開瀏覽，不需登入。
- 編輯：使用 Google 帳號登入，後端驗證 Google ID token 後，再比對 `Editors` 工作表 Email 白名單。
- 不自行建立帳號或密碼。

## 2. Editors 工作表

欄位：

`editor_id | name | email | active | note`

只有 `active=TRUE` 且 Email 完全符合的 Google 帳號可編輯。
人員離開專案時，將 active 改為 FALSE，不要刪除舊資料。

## 3. Documents 可見性

`visibility` 有三種：

- `public`：公開。未登入訪客也能看到並開啟。
- `internal`：內部。只有 Email 白名單登入成功者能看到／開啟。
- `restricted`：受限。網站可顯示「有這份文件」及基本 metadata，但不直接提供檔案連結。適合租借正式契約、敏感合約等。

建議：
- 姊妹鐵路締結書：視實際公開政策，可設 public。
- 一般續約 LOI/MOU：依內容判斷 public 或 internal。
- 車輛租借正式契約：預設 internal 或 restricted。

## 4. Google Drive 權限要同步設定

網站的 visibility 只是第一層。真正的 PDF 也必須在 Google Drive 設對分享權限：

- public：可依單位政策開放知道連結者檢視。
- internal：Drive 本身也只分享給允許的人或適當群組。
- restricted：不要把公開 Drive URL 提供給前台；可只留內部管理連結。

如果 Drive 本身設成「任何知道連結的人」，即使網站把按鈕藏起來，連結仍可能被轉傳，因此不能只靠前端隱藏。

## 5. Apps Script 指令碼屬性

設定：

- `GOOGLE_CLIENT_ID`：Google Identity Services Web Client ID。

前端之後也需要填入同一組 Client ID，Google 登入取得的 ID token 會送到 Apps Script 後端驗證。
