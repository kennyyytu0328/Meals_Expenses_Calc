# MealWise AI — 智慧餐費結帳系統 (BYOK)

簡短描述  
這是單檔 HTML 的軽量 Web App，用於上傳單頁 PDF 的餐費報表，透過 Google Generative AI 的 Gemini 模型解析表格並回傳 JSON 結構，於前端呈現並允許輸入實收金額來計算找零與匯總統計。

主要檔案
- [index.html](index.html) — 應用程式完整程式碼（React + PDF.js + Google Generative AI）。

特色
- BYOK（Bring Your Own Key）：API Key 儲存在使用者瀏覽器 localStorage（item key: `mealwise_gemini_key`）。
- 使用 PDF.js 將第一頁 PDF 轉成 base64 圖像並發送給 Gemini 模型進行表格擷取。
- 解析結果會顯示為可互動的表格，允許輸入已收金額並自動計算找零與總計。
- UI 使用 Tailwind CSS + Lucide Icons。

如何運作（程式碼參考）
- UI 與主流程在 [`App`](index.html) 中控制。
- 檔案上傳處理：[`handleFileUpload`](index.html)。
- PDF->Image：[`pdfToImage`](index.html)（透過 PDF.js，僅將第 1 頁渲染成 JPEG base64）。
- 呼叫模型與處理回傳：[`processPDF`](index.html)（使用 `GoogleGenerativeAI`、`gemini-2.5-flash`）。
- 已收金額輸入與變更：[`handleReceivedChange`](index.html)。
- Lucide 圖示小元件：[`Icon`](index.html)。

先決條件
- 現代瀏覽器（支援 import maps、ES Modules 的 Chromium、Firefox、Safari 新版）。
- 有效的 Google Generative AI（Gemini）API Key（可從 Google AI Studio 取得：https://aistudio.google.com/app/apikey）。
- 建議透過簡單本地 HTTP Server 開啟（避免 local file 的限制或 import map 問題）。

本機啟動（快速測試方式）
- 方法 A：使用 Node.js `http-server`
  1. 安裝 (一次性): `npm install -g http-server`
  2. 啟動: `http-server . -p 8080`
  3. 開啟瀏覽器並前往 `http://localhost:8080/`

- 方法 B：使用 Python（若有安裝）
  - Python 3:
    - `python -m http.server 8080`
    - 開啟瀏覽器並前往 `http://localhost:8080/`

使用教學
1. 以前述方式啟動本地 server 並開啟 [index.html](index.html)。
2. 在右上或左側「設定 Gemini API Key」畫面輸入您的 API Key（Key 儲存在 localStorage，Key 名稱: `mealwise_gemini_key`）。
3. 上傳 PDF（單頁報表）至側邊上傳區塊（僅接受 `.pdf`）。
4. 等待 AI 解析（進度會顯示中）。
5. 解析完成後，會以由高到低排序排名及 `due`（應收金額）列出各員工；可於「已收金額」輸入欄位輸入實收金額以計算找零。
6. 可以按「重設 Key」以清除本地儲存與回到設定畫面。

注意事項 / 限制
- 只支援單頁 PDF 的第一頁解析（在 [`pdfToImage`](index.html)）— 若要支援多頁，需擴充迴圈/頁面管理。
- `gemini-2.5-flash` 為示範模型名稱，若無法使用請確認您的 Key/權限或將 model 參數改為可用模型。
- 請勿在公開場所公開您的 API Key；Key 只存在 localStorage（BYOK）。
- 圖像轉換品質與 OCR 效果依 PDF 原始品質而定；若報表有複雜格式或掃描模糊，解析結果可能會有錯誤。
- JSON 回傳的格式需為純 JSON 陣列，若 Gemini 輸出多餘 Markdown，程式會嘗試清理掉多餘標記（參考 [`processPDF`](index.html) 的 clean-up 程式）。

除錯與常見問題
- 「解析失敗」並提示 API Key：請確認 Key 有效與權限與額度。
- 無法上傳檔案：請確認上傳檔為 PDF（`accept=".pdf"`）。
- 無法載入 importmap 模組：請使用現代瀏覽器並以 HTTP Server 執行（避免 file://）。
- 解析資料格式不符：確認 Gemini 回傳 JSON 格式（參考 prompt 中要求）。

建議改進方向
- 增加多頁 PDF 支援。
- 加強錯誤回報與欄位驗證（例如浮點數、貨幣格式）。
- 加入範例 PDF 範本與單元測試。
- 客製化模型與 prompt 以提升表格擷取精準度。

隱私與安全
- Key 儲存在 localStorage；系統不會將 Key 儲存至任何伺服器或與第三方共享（請勿在共用裝置使用您的 Key）。
- 此專案僅作為範例與內部工具使用，請自行評估商業使用時的安全性、合規與成本。

貢獻
- 若欲提交功能或修正，請 Fork repo 並提出 Pull Request。建議先發起 Issue 討論設計與需求。

授權
- MIT License（如需更改請自行新增 license）。

參考/連結
- [index.html](index.html) — 程式主檔案（包含 React/JSX、PDF.js、Lucide、Google Generative AI）。
- Google AI Studio API Key 申請: https://aistudio.google.com/app/apikey
