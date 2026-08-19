# 阿里山林鐵｜國際鐵道交流合作歷程

GitHub Pages Prototype + Google Sheet / Apps Script backend starter。

## 目前前台功能

- 國際姊妹鐵路／友好交流鐵路分類
- 11 條姊妹鐵路資料
- 合作歷程時間軸
- 關聯先前紀錄／合作串
- 精簡新增表單
- 編輯人名單
- 合作文件中心（締結、續約、LOI、租借／正式合約等）
- Word 匯出功能入口（後續階段）

## GitHub Pages 上線

1. 建立一個新的 GitHub repository。
2. 將本資料夾內容上傳至 repository 根目錄。
3. Repository → Settings → Pages。
4. Source 選 `Deploy from a branch`。
5. Branch 選 `main`，Folder 選 `/ (root)`。
6. 儲存後等待 GitHub Pages 產生網址。

## 後台設定

請閱讀：[`SETUP-GUIDE.md`](SETUP-GUIDE.md)

後台起始程式：

- `backend/apps-script/Code.gs`
- `backend/apps-script/appsscript.json`

Sheet 欄位範例：

- `data-templates/Railways.csv`
- `data-templates/Records.csv`
- `data-templates/Editors.csv`
- `data-templates/Documents.csv`

## 建議架構

GitHub Pages → Google Apps Script Web App → Google Sheet

正式 PDF／協議文件 → Google Drive

網站只保存文件索引與 Drive 連結，不建議把所有正式 PDF 直接放進 GitHub repository。

## Prototype 提醒

目前 `index.html` 仍使用內建示意資料與 localStorage，目的是先確認操作流程與版面。`backend` 已備妥下一階段串接 Google Sheet 所需的起始程式，但尚未在前台寫死任何 Web App URL 或編輯碼。
