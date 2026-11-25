# vito-lin-dev

- GitHub：https://github.com/vito-lin-dev
- Discord（TAIONE vLLM contributions）：vv0925vv
- Slack: Vito (https://opensource4you.slack.com/team/U0CLD6BAHDK)

## 2026-11-24

提名人 tjtanaa · 建議等級 2 萬

- [x] 關係人宣告：提名人與被提名者無親屬、僱傭、財務、業務合作或其他可能影響公正判斷之利害關係

- 找到 `--shutdown-timeout N` 的 bug：文件寫 timeout 到了會 abort，實際上還在跑的 request 收不到最後一個 chunk，`vllm serve` 也不會結束。root cause 是 EngineCore 的 drain 沒有 deadline，而 process manager 在 `N` 秒就 kill EngineCore。先試過在 API server 做，發現排隊中的 request 會被丟掉，才改在 EngineCore 做，描述裡寫了原因。附一個在 nightly 會 fail 的 test（[#64991](https://github.com/vllm-project/vllm/pull/64991)，+108/−5，review 中）
- review 回覆快而且到位：kliuae 11/16 指出第二個 signal 會重設 deadline，隔天修掉（commit `4609e95ded`），另一則說明 Rust frontend 的 shutdown 順序不同並把 test 標成 skip（[#64991 review](https://github.com/vllm-project/vllm/pull/64991#pullrequestreview)）
- Rust frontend 第一個 merge 的 PR：tool parsing 關掉時回 400，不再回 500。code 一行，HTTP 和 gRPC 各補一個 test（[#63580](https://github.com/vllm-project/vllm/pull/63580)，11/14 merge）。同一題開了 issue [#63584](https://github.com/vllm-project/vllm/issues/63584)，後續 PR [#64069](https://github.com/vllm-project/vllm/pull/64069) 在啟動時加 warning
- 找到 Rust frontend 讀 Hugging Face cache 的問題：cache 裡只有 tokenizer 的 snapshot 時，`generation_config.json` 被當成不存在，Kimi-K2.5 的 `<|im_end|>` 不在 stop set 裡，也沒有任何 log。查了自己 cache 的 25 個 model，10 個受影響；vLLM 自己的三個 roundtrip test 在新機器上也因此 fail（[#64768](https://github.com/vllm-project/vllm/pull/64768)，+471/−12，待 rebase）
- [#63649](https://github.com/vllm-project/vllm/pull/63649)（Rust OpenAI routes 檢查 `max_tokens`）開出後，[#63964](https://github.com/vllm-project/vllm/pull/63964) 做了同一個檢查並多涵蓋 render routes，描述裡註明與 #63649 重疊，10/28 merge。v 自己關掉 #63649 並留言說明
- 把被關掉的 [#43208](https://github.com/vllm-project/vllm/pull/43208)（Kubernetes drain shutdown 文件）接起來，照實測結果修正內容，並回覆社群成員的 review（[#64995](https://github.com/vllm-project/vllm/pull/64995)）
- PR 描述固定寫 Problem / Fix / Unchanged / 給 reviewer 的問題，附修改前後的對照表，以及每個 test 在拿掉哪個條件時會 fail
