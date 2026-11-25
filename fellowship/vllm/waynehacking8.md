# waynehacking8

- GitHub：https://github.com/waynehacking8
- Discord（TAIONE vLLM contributions）：waynehacking8
- Slack: WEI CHENG CHIU (https://opensource4you.slack.com/team/U0CLTKPPMLZ)

## 2026-11-24

提名人 tjtanaa · 建議等級 2 萬

- [x] 關係人宣告：提名人與被提名者無親屬、僱傭、財務、業務合作或其他可能影響公正判斷之利害關係

- vLLM 已 merge 7 個 PR（[清單](https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged+author%3Awaynehacking8)）。最近兩個：把 MiniMax-M3 Hopper sparse prefill kernel 裡的 `tl.make_block_ptr` 拿掉（[#66099](https://github.com/vllm-project/vllm/pull/66099)，11/18 merge）；`VLLM_USE_FASTOKENS` 要求 fastokens >= 0.3.5（[#65679](https://github.com/vllm-project/vllm/pull/65679)，11/5 merge）
- 找到 Rust frontend 用 fastokens 0.3.4 之前的 split cache 會產生錯的 token id，附重現步驟和修法（[#63467](https://github.com/vllm-project/vllm/issues/63467)）。修正在 [#63707](https://github.com/vllm-project/vllm/pull/63707) merge，PR 連到這個 issue。之後提了 RFC，主張把「token id 和 Python frontend 一致」變成有測試的約定（[#65797](https://github.com/vllm-project/vllm/issues/65797)），並為了讓討論集中在 RFC，自己關掉 1,243 行的測試 PR（[#65757](https://github.com/vllm-project/vllm/pull/65757)）
- 在 SM120（RTX PRO 6000）上找出 `flashinfer_b12x` NVFP4 MoE 從 #64692 之後輸出亂碼，原因是 DeepGEMM warmup 蓋掉 FC2 input scale（[#67914](https://github.com/vllm-project/vllm/issues/67914)），並送出修正（[#68082](https://github.com/vllm-project/vllm/pull/68082)，review 中）
- block-FP8 Triton GEMM 的 row offset 改成 64-bit（[#66200](https://github.com/vllm-project/vllm/pull/66200)，review 中）。另一位貢獻者在 NVIDIA L4 上獨立重現了同一個 overflow（[留言](https://github.com/vllm-project/vllm/pull/66200#issuecomment-6968406270)）
- Review 別人的 PR 22 個（[清單](https://github.com/vllm-project/vllm/pulls?q=is%3Apr+reviewed-by%3Awaynehacking8+-author%3Awaynehacking8)），多數是 quantization、MoE 和 SM12x kernel。例：在 DGX Spark（GB10, sm_121）上把 [#68521](https://github.com/vllm-project/vllm/pull/68521) 的整個測試檔在 main 和 PR 各跑一次再回報，PR 於 11/23 merge；review [#54316](https://github.com/vllm-project/vllm/pull/54316) 之後，另一位貢獻者開了 [#69192](https://github.com/vllm-project/vllm/pull/69192)，描述裡引用了這次 review
- 接了 track lead 10/3 給的 SM12x issue，在 issue 上回報進度（[#58624](https://github.com/vllm-project/vllm/issues/58624)、[#61239](https://github.com/vllm-project/vllm/issues/61239)）
- vLLM 之外，同一條 SM120 kernel 線在 FlashInfer merge 了 10 個 PR（[清單](https://github.com/flashinfer-ai/flashinfer/pulls?q=is%3Apr+is%3Amerged+author%3Awaynehacking8)），LMCache、llm-compressor、compressed-tensors 各 1 個
- 在 Discord 回答其他成員的問題：v0 engine 已移除、從 `vllm/v1/engine/core.py` 讀起；TPU 的 code 在 `vllm-project/tpu-inference`（[訊息](https://discord.com/channels/1550434326808764496/1550434327362408495/1571794968723591231)，11/16）