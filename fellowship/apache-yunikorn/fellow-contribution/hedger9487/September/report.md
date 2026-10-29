# Monthly Contribution Report

**GitHub ID:** [hedger9487](https://github.com/hedger9487)  
**Jira ID:** [hedger9487](https://issues.apache.org/jira/secure/ViewProfile.jspa?name=hedger9487)  
**Slack:** [@hedger](https://opensource4you.slack.com/team/U0BUKKVB7DF)  
**Report Period:** 2026-09-03 to 2026-10-09

## Summary

- **PR:** 25 (16 merged, 9 in review)
- **Issue Triage / Filed JIRAs:** 23

---

## Must-read PRs

這一個月主要都在搞 `yunikorn-core` 的 **Preemption（搶佔）** 。一開始其實只是在看 `YUNIKORN-3137`，但順著code追下去之後，發現從一開始算資源夠不夠搶、中間在節點上挑要踢掉哪些victim，到最後檢查 queue 配額，整條路上其實藏了不少神奇的問題，於是就一個一個搞下去：

1. **當大任務要搶多個小 pod、或遇到 Anti-Affinity 時卡死的問題：[[YUNIKORN-3137]](https://github.com/apache/yunikorn-core/pull/1148) & [[YUNIKORN-3442]](https://github.com/apache/yunikorn-core/pull/1156)** (`Merged`)
   - **遇到的問題：** 在把節點上挑出來的候選 pod 交給 k8shim 檢查 K8s 排程規則（`PreemptionPredicates`）時，發現兩個會讓任務永遠排不上去的情況：
     1. `YUNIKORN-3137`：當一個大 pod 需要同時看多種資源（例如 CPU 和 Memory），並且得踢掉超過 2 個小 pod 才塞得下時，原本比對資源大小的寫法會誤判，導致永遠挑不出足夠的 pod。
     2. `YUNIKORN-3442`：原本程式只要湊齊資源數量，就會把候選清單直接切斷（只傳前幾個 pod 給 k8shim）。結果如果排在前面的候選 pod 剛好因為 `PodAntiAffinity` 不能被搶，後面明明還有其他可以搶的 pod，k8shim 卻看不到，任務就這樣一直卡住。
   - **怎麼修：** 修好資源比較的判斷邏輯，並且把完整的候選清單傳給 k8shim，讓前面的候選 pod 檢查不過時，還能繼續往後找替補。

2. **明明資源夠卻提早放棄搶佔，導致大任務一直乾等：[[YUNIKORN-3449]](https://github.com/apache/yunikorn-core/pull/1157)、[[YUNIKORN-3450]](https://github.com/apache/yunikorn-core/pull/1163)、[[YUNIKORN-3458]](https://github.com/apache/yunikorn-core/pull/1161) & [[YUNIKORN-3463]](https://github.com/apache/yunikorn-core/pull/1164)** (`Merged`)
   - **遇到的問題：** 追完前面的問題後繼續往上追，發現有好幾個地方會讓搶佔「提早放棄」：
     1. 在一開始評估「就算去搶別人，資源夠不夠塞下這個任務」時，漏算了 queue 自己還沒用完的 guaranteed 額度（`3450`），也漏算了節點上原本就空著的資源（`3449`），導致明明「空閒資源 + 搶來的資源」就夠了，排程器卻誤以為不夠而直接放棄。
     2. 在節點上一個個檢查哪些 pod 可以搶時（`3458` 的 `calculateVictimsByNode` 和 `3463` 的 `calculateAdditionalVictims`），只要剛好碰到一個「它所屬的 queue 不能再讓出資源」的 pod，迴圈就直接 `break` 跳出，導致排在它後面、來自其他 queue 的合法 pod 完全沒機會被檢查到。
   - **怎麼修：** 把漏算的 queue guaranteed 餘額和節點空閒空間補進公式裡；並把迴圈裡的 `break` 改掉，遇到不能搶的 pod 時只跳過它自己，繼續檢查同節點上的其他 pod。

3. **算搶佔方 Queue 會不會超額時，誤把「被搶的 Pod 大小」加進去：[[YUNIKORN-3459]](https://github.com/apache/yunikorn-core/pull/1170) & [[YUNIKORN-3478]](https://github.com/apache/yunikorn-core/pull/1172)** (`Merged`)
   - **遇到的問題：** 在修上面迴圈的時候又發現一個很微妙的邏輯：當排程器在模擬「搶了這些 pod 之後，搶佔方自己的 queue 會不會超過 `max` 上限」時，原本的寫法是每挑一個 victim，就把「那個 victim 的大小」加進搶佔方 queue 的用量裡，而不是加「搶佔方自己要申請的 pod 大小」。這會造成如果一個要 2G 的任務去搶一個 8G 的大 pod，搶佔方 queue 會被當成要多用 8G 而被誤判超額擋掉。
   - **怎麼修：** 在 `calculateVictimsByNode`（`3459`）和 `calculateAdditionalVictims`（`3478`）裡把兩邊的帳分開算：被搶的 queue 扣掉 victim 的大小，而搶佔方的 queue 則是固定檢查自己要的 pod 大小會不會超過上限。

---

## PR

### Merged (16)

#### Core Preemption Engine (`apache/yunikorn-core`)
- [[YUNIKORN-3491] Remove obsolete TestTryPreemption_PrematureAdditionalVictimsLoopTermination](https://github.com/apache/yunikorn-core/pull/1181): 在 `YUNIKORN-3478` 改完配額算法後，把已經不再適用的舊測試案例清掉。
- [[YUNIKORN-3478] Decouple ask queue quota accounting from victim sizes in calculateAdditionalVictims](https://github.com/apache/yunikorn-core/pull/1172): 修正 `calculateAdditionalVictims` 檢查搶佔方 queue 上限時誤加 victim 大小的問題，改用任務本身的需求量來檢查。
- [[YUNIKORN-3459] Decouple ask queue quota accounting from victim sizes in calculateVictimsByNode](https://github.com/apache/yunikorn-core/pull/1170): 修正 `calculateVictimsByNode` 誤把被搶 pod 的大小加進搶佔方 queue 用量的問題。
- [[YUNIKORN-3463] Premature loop abort in calculateAdditionalVictims starves preemption](https://github.com/apache/yunikorn-core/pull/1164): 修掉 `calculateAdditionalVictims` 遇到單一不能搶的 pod 就直接跳出整個迴圈的問題，讓後面的候選 pod 可以繼續被檢查。
- [[YUNIKORN-3450] False preemption shortfall aborts queue preemption and starves large tasks despite sufficient guaranteed quota](https://github.com/apache/yunikorn-core/pull/1163): 計算搶佔缺口時把 queue 現有還沒用完的 guaranteed 額度也算進來，避免大任務明明配額夠卻無法觸發搶佔。
- [[YUNIKORN-3458] Premature victim loop termination in calculateVictimsByNode starves preemption](https://github.com/apache/yunikorn-core/pull/1161): 修掉 `calculateVictimsByNode` 遇到單一不能讓出資源的 pod 就提早結束搜尋的問題。
- [[YUNIKORN-3449] Preemption falsely aborts and starves large asks by ignoring node available capacity in shortfall check](https://github.com/apache/yunikorn-core/pull/1157): 在評估搶佔資源夠不夠用時，把節點上原本剩下的空閒資源也算進去，不再白白放棄其實塞得下的節點。
- [[YUNIKORN-3442] Fix preemption victim selection vector truncation for predicate constraints](https://github.com/apache/yunikorn-core/pull/1156): 不要在呼叫 k8shim 檢查規則前就把候選 pod 清單切斷，讓遇到 anti-affinity 時還能往後找其他替補 pod。
- [[YUNIKORN-3137] Fix preemption victim selection vector comparison flaw for larger asks](https://github.com/apache/yunikorn-core/pull/1148): 修正比較多種資源時的邏輯錯誤，解決大任務需要搶超過 2 個小 pod 時會失敗的問題。

#### Concurrency, Lock Safety & Quota Accounting (`apache/yunikorn-core`)
- [[YUNIKORN-3446] Race condition in ensureGroupTrackerForApp creates duplicate GroupTrackers and bypasses group quota](https://github.com/apache/yunikorn-core/pull/1154): 修掉 `ensureGroupTrackerForApp` 換拿寫鎖後沒有再檢查一次的 race condition，避免同時建出重複的 `GroupTracker` 導致群組配額限制失效。
- [[YUNIKORN-3379] Fix scheduler panic on PLACEHOLDER_REPLACED release without linked replacement](https://github.com/apache/yunikorn-core/pull/1150): 當 placeholder 被釋放但沒有對應的 real allocation 時加上 nil 檢查，避免直接存取空指標造成整個 scheduler crash。
- [[YUNIKORN-3380] Fix user/group tracker quota leak on RemoveAllAllocations with pending asks](https://github.com/apache/yunikorn-core/pull/1147): 修正移除還有等待中任務（pending asks）的應用程式時，使用者與群組配額沒有扣乾淨的問題。
- [[YUNIKORN-3409] Fix concurrent map read and write on rejectedApplications](https://github.com/apache/yunikorn-core/pull/1144): 為 `PartitionContext.rejectedApplications` 補上鎖保護，避免同時讀寫 map 造成 crash。

#### Kubernetes Shim & Predicate Integration (`apache/yunikorn-k8shim`)
- [[YUNIKORN-3464] PreemptionFilter fails for affinity and topology predicates due to stale CycleState](https://github.com/apache/yunikorn-k8shim/pull/1098): 在 `PreemptionFilter` 模擬移除 pod 時先複製一份 `CycleState` 並呼叫 `RemovePod`，讓 affinity 與 topology spread 插件能拿到更新後的節點狀態。
- [[YUNIKORN-3381] Do not adopt terminated orphaned foreign pods on node registration](https://github.com/apache/yunikorn-k8shim/pull/1088): 當節點比較晚註冊時，不要把已經跑完（`Succeeded` / `Failed`）的非 YuniKorn pod 加進快取，避免節點容量一直被死掉的 pod 佔住。
- [[YUNIKORN-3431] Add regression test coverage for KubernetesShim scheduling loop shutdown](https://github.com/apache/yunikorn-k8shim/pull/1085): 幫 `KubernetesShim` 停止背景排程迴圈的 `Stop()` 流程補上單元測試。

---

### In Review (9)

- [[YUNIKORN-3506] Fix child queue preemptable resource calculation in quota preemption](https://github.com/apache/yunikorn-core/pull/1189): 修正 `QuotaPreemptor` 在加總子 queue 可被搶佔的資源時，因為只相減部分資源欄位而漏算超額資源的問題。
- [[YUNIKORN-3501] Account for node available resource during victim selection in RequiredNodePreemptor.GetVictims](https://github.com/apache/yunikorn-core/pull/1188): 在 DaemonSet 指定節點搶佔（`RequiredNodePreemptor`）挑 victim 前先扣掉節點現有的空閒資源，避免多殺不必要的 pod。
- [[YUNIKORN-3499] Exempt shared ancestors in QueuePreemptionSnapshot.GetPreemptableResource for sibling preemption](https://github.com/apache/yunikorn-core/pull/1187): 在往上檢查父 queue 可搶佔額度時，跳過搶佔雙方共用的上層父 queue，避免同層級的兄弟 queue 互相搶佔時被共同父 queue 擋住。
- [[YUNIKORN-3496] Preemption can drop victim queue below guaranteed quota in calculateAdditionalVictims](https://github.com/apache/yunikorn-core/pull/1186): 在 `calculateAdditionalVictims` 挑人時逐一檢查被搶的 queue 剩餘額度夠不夠扣，避免一次搶了一個大 pod 結果讓對方掉到 guaranteed 保障額度以下。
- [[YUNIKORN-3492] Preemption can drop victim queue below guaranteed quota in calculateVictimsByNode](https://github.com/apache/yunikorn-core/pull/1185): 在 `calculateVictimsByNode` 同樣補上每個 victim 對 queue guaranteed 下限的檢查，防止多搶導致跌破保障額度。
- [[YUNIKORN-3493] Eliminate redundant second pass and snapshot cloning in calculateVictimsByNode](https://github.com/apache/yunikorn-core/pull/1183): 在 `YUNIKORN-3459` / `3478` 修好配額算法後，把 `calculateVictimsByNode` 裡已經不需要的第二輪重複檢查跟 map 複製拿掉，讓程式碼更乾淨。
- [[YUNIKORN-3479] Optimize preemption victim selection and trimming to mitigate over-preemption and collateral waste](https://github.com/apache/yunikorn-core/pull/1178): 選victim時考慮到如何減少資源浪費，使用cosine similarity * magnitude 挑選，在選完 victim 後從後面往回檢查一次，如果後面選到的大 pod 已經夠用了，就把前面多選的小 pod 從名單中移掉，少殺一點無辜的 pod。
- [[YUNIKORN-3410] Fix self-deadlock on ClusterContext lock during partition removal](https://github.com/apache/yunikorn-core/pull/1145): 修正移除 partition 時拿著 `ClusterContext` 寫鎖去跑清理流程可能造成的 deadlock 問題。
- [[YUNIKORN-3465] Add victimAllocationKeys in PreemptionPredicatesResponse for minimal preemption](https://github.com/apache/yunikorn-scheduler-interface/pull/176): 在 `PreemptionPredicatesResponse` 介面新增 `victimAllocationKeys` 欄位，讓 k8shim 可以直接回傳真正需要被搶的最小 pod 清單。

---

## Issue Triage & Bug Discovery (23 JIRAs Filed)

這段時間在讀 code 跟解 bug 的過程中，一共找出並開了 **23 張 JIRA issues**：

- **[YUNIKORN-3508](https://issues.apache.org/jira/browse/YUNIKORN-3508)**: `Queue.stateTime` 在更新狀態時沒有拿 queue lock 的問題
- **[YUNIKORN-3507](https://issues.apache.org/jira/browse/YUNIKORN-3507)**: `QuotaPreemptor` 算可搶資源時沒有扣掉「已經在搶佔中」的資源
- **[YUNIKORN-3506](https://issues.apache.org/jira/browse/YUNIKORN-3506)**: `QuotaPreemptor` 算子 queue 可搶佔資源時漏算部分資源類型的問題
- **[YUNIKORN-3502](https://issues.apache.org/jira/browse/YUNIKORN-3502)**: `RequiredNodePreemptor.GetVictims` 沒有跳過對目前欠缺的資源沒幫助的 pod
- **[YUNIKORN-3501](https://issues.apache.org/jira/browse/YUNIKORN-3501)**: `RequiredNodePreemptor.GetVictims` 挑 victim 時沒算到節點原本空著的資源，導致多殺 pod
- **[YUNIKORN-3500](https://issues.apache.org/jira/browse/YUNIKORN-3500)**: `GetRemainingGuaranteedResource` 用字串開頭比對路徑，導致像 `root.a` 和 `root.a-1` 被誤當成父子 queue
- **[YUNIKORN-3499](https://issues.apache.org/jira/browse/YUNIKORN-3499)**: `GetPreemptableResource` 沒跳過共同父 queue 導致同層 queue 無法互相搶佔
- **[YUNIKORN-3497](https://issues.apache.org/jira/browse/YUNIKORN-3497)**: 討論當 queue 裡只有一個大 pod 剛好壓在 guaranteed 邊界上時該怎麼處理搶佔
- **[YUNIKORN-3496](https://issues.apache.org/jira/browse/YUNIKORN-3496)**: `calculateAdditionalVictims` 可能會讓被搶的 queue 掉到 guaranteed 額度以下
- **[YUNIKORN-3493](https://issues.apache.org/jira/browse/YUNIKORN-3493)**: 拿掉 `calculateVictimsByNode` 裡多餘的第二輪檢查與 snapshot 複製
- **[YUNIKORN-3492](https://issues.apache.org/jira/browse/YUNIKORN-3492)**: `calculateVictimsByNode` 可能會讓被搶的 queue 掉到 guaranteed 額度以下
- **[YUNIKORN-3479](https://issues.apache.org/jira/browse/YUNIKORN-3479)**: 調整 victim 挑選與回頭剔除多餘小 pod 的邏輯，減少多殺 pod
- **[YUNIKORN-3478](https://issues.apache.org/jira/browse/YUNIKORN-3478)**: 修正在 `calculateAdditionalVictims` 裡誤拿 victim 大小去算搶佔方 queue 配額的問題
- **[YUNIKORN-3465](https://issues.apache.org/jira/browse/YUNIKORN-3465)**: 在 `PreemptionPredicatesResponse` 新增 `victimAllocationKeys` 欄位
- **[YUNIKORN-3464](https://issues.apache.org/jira/browse/YUNIKORN-3464)**: `PreemptionFilter` 共用舊的 `CycleState` 導致 affinity 與 topology 插件誤判
- **[YUNIKORN-3463](https://issues.apache.org/jira/browse/YUNIKORN-3463)**: `calculateAdditionalVictims` 迴圈太早 `break` 導致後面的 pod 沒被檢查到
- **[YUNIKORN-3460](https://issues.apache.org/jira/browse/YUNIKORN-3460)**: 評估在 k8shim 搶佔流程中少殺一點非必要 victim pods 的做法
- **[YUNIKORN-3459](https://issues.apache.org/jira/browse/YUNIKORN-3459)**: 修正在 `calculateVictimsByNode` 裡誤拿 victim 大小去算搶佔方 queue 配額的問題
- **[YUNIKORN-3458](https://issues.apache.org/jira/browse/YUNIKORN-3458)**: `calculateVictimsByNode` 迴圈太早終止導致後面的候選 pod 被跳過
- **[YUNIKORN-3450](https://issues.apache.org/jira/browse/YUNIKORN-3450)**: 算搶佔缺口時漏算 queue 還沒用完的 guaranteed 額度，害大任務無法觸發搶佔
- **[YUNIKORN-3449](https://issues.apache.org/jira/browse/YUNIKORN-3449)**: 檢查搶佔資源夠不夠時漏算節點原本空閒的空間，害大任務提早放棄
- **[YUNIKORN-3446](https://issues.apache.org/jira/browse/YUNIKORN-3446)**: `ensureGroupTrackerForApp` 在高併發下可能建出重複 `GroupTracker` 導致群組配額失效
- **[YUNIKORN-3442](https://issues.apache.org/jira/browse/YUNIKORN-3442)**: 候選 victim 清單太早切斷，導致遇到 pod anti-affinity 時沒辦法往後找替補 pod
