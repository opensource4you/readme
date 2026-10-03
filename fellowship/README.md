# Fellowship 提名紀錄

這裡記錄 TAIONE「AI 開源新勢力」計畫的提名。

一句話說明：**各組 lead 覺得某人累積的功績夠格了，就開 PR 提名記錄下來，並標注建議等級（2 萬或 3 萬）。這份紀錄提供給 TAIONE 作為發放獎勵金的參考。**

這裡只做提名和記錄，不處理錢。實際發放由 TAIONE 決定。

## 流程

1. lead 覺得某人累積的功績夠格了，就開 PR，在 `<track>/<github-id>.md` 加上這次的事蹟和建議等級。隨時都可以提，不用等固定日期。
2. 嘉平看過後合併。
3. 合併後的紀錄提供給 TAIONE 參考。

開 PR 時記得 ping 嘉平 review。

## 建議等級

- 一般：2 萬
- 特別猛：3 萬。寫一句理由加一個連結。
    - lead 自己標，嘉平只會往下調，不會往上調。
    - 3 萬大約控制在三分之一以內。如果一組每個人每次都是 3 萬，那就不是特別猛了。
    - 拿不定就標 2 萬，下次再提就好。

## 事蹟怎麼寫

用「事蹟」的方式寫，不要寫「從 X 月開始累積了 N 個 PR」。這樣不用擔心重複申報，以前的貢獻也可以寫進來。

每條事蹟後面附一個連結（PR、issue、GitHub search、Slack 討論都可以），方便查證。

每次提名只寫上次提名之後的新事蹟。

寫法範例：

- 持續維持每週 2 PRs、4 reviews 的節奏（[PR 清單](https://github.com/apache/airflow/pulls?q=author%3Aexample)）
- 完成 xxx 功能（[#12345](https://github.com/apache/airflow/pull/12345)）
- 幫忙撰寫課程教材（連結）
- 每週在 Slack 上帶 3 次以上的討論（連結）

## 目錄結構

```
fellowship/
├── README.md              # 這份說明
├── apache-airflow/
│   ├── README.md          # 各組自己的補充說明，沒有也可以
│   └── <github-id>.md     # 一人一檔
├── kuberay/
├── flyte/
└── ...
```

依專案切目錄，名稱和 Slack 頻道一致。一人一檔，檔名用 GitHub ID，新的提名加在檔案最上面，最新的在最前。

## 檔案格式

```markdown
# <github-id>

- GitHub：https://github.com/<github-id>
- Slack：<Slack 顯示名稱或 profile 連結>

## 2026-11-20

- 提名人：
- 建議等級：3 萬，<一句理由>（連結）
- 事蹟：
  - ...

## 2026-10-07

- 提名人：<lead 的 GitHub ID>
- 建議等級：2 萬
- 事蹟：
  - ...（附連結）
  - ...
```

## 不放在這裡的東西

- 有沒有發、什麼時候發、發多少。這些由 TAIONE 處理，不寫進 GitHub。
- 被退件的提名。沒有合併的 PR 直接關掉就好。