# Markdown To Slide

本次使用 Codex 與內建的 compute use 協助製作。

1. introduction.md：完整的純文字講稿。
2. slides.md：完整的 Marp 簡報，包含樣式、HTML／SVG 示意圖與講者備註。

直接把 introduction 丟給 AI 讀並下 prompt：

```
使用 Marp。
優先使用圖表、流程圖和具體案例。
保留重要技術細節，補充 Speaker Notes。
建立 slides.md，並確保可以在本地預覽。
匯出 PDF 與 PPTX，檢查是否有文字溢出或排版錯誤。
```

之後就是逐個檢視跟請 AI 調整流程圖，以提供更好的排版、說明跟示意圖。

## 匯出

需要 Node.js 18 以上與 Chrome、Edge 或 Firefox。第一次執行需下載 Marp CLI。

標準 Marp PPTX 以投影片圖片保存版面，不能在 PowerPoint 中逐一修改文字或圖表。請修改 `slides.md` 後重新匯出；流程圖、表格與樣式皆保留在原始碼中。

在本資料夾執行：

```sh
# 可離線開啟的 HTML 簡報
npx @marp-team/marp-cli@4.5.1 slides.md --html -o slides.html

# PDF：保留書籤，不顯示講者備註圖示
npx @marp-team/marp-cli@4.5.1 slides.md --html --pdf --pdf-outlines -o slides.pdf

# PowerPoint：包含 Speaker Notes
npx @marp-team/marp-cli@4.5.1 slides.md --html --pptx -o slides.pptx
```

## 編輯與即時預覽

**只需編輯 `slides.md`**。

使用 CLI 的監看模式，Marp 會自動重新產生 `slides.html`：

```sh
npx @marp-team/marp-cli@4.5.1 slides.md --html --watch -o slides.html
```

編輯完再視需求生成 pdf, pptx，也可直接本地開啟 html。
