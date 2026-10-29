> **送出 PR 前請刪掉這段說明，只留下你要寫的內容。**

請先確認這個 PR 屬於哪一種，照對應規則放檔案：

### 加 project 資訊
放 `mentor-projects-information/`，並更新 README.md

### 加文章
- 外部文章：在 README.md 加連結
- 放 repo 裡：`articles/<自訂目錄>/README.md`，圖片盡量用外部圖床，並更新 README.md

### Fellowship 提名（lead 專用）
- 檔案：`fellowship/<track>/<github-id>.md`，一人一檔，新提名放最上方
- 關係人宣告：每次提名都要填。勾選代表你和被提名者沒有親屬、僱傭、財務、業務合作等利害關係；有的話不勾，底下補一行說明。沒填的不會 merge
- 建議等級 2 萬或 3 萬；3 萬在最後加 `### 3 萬理由`，一句話就好
- 每次提名照這個格式：
  ```
  ## 2026-10-01

  提名人 <github-id> · 建議等級 2 萬

  - [x] 關係人宣告：提名人與被提名者無親屬、僱傭、財務、業務合作或其他可能影響公正判斷之利害關係

  - 事蹟一（連結）
  - 事蹟二（連結）
  ```
- PR 內文請保留 `@chia7712`，讓我收到通知。細節見 [fellowship/README.md](../tree/main/fellowship)

### 校園課程
- 教材：`campus-courses/<技術>/`，格式不限
- 作業：`campus-courses/submissions/<課程>/<github-id>.md`，一人一檔，整學期更新同一個
- 細節見 [campus-courses/README.md](../tree/main/campus-courses)
