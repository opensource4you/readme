# Apache YuniKorn 
(本文件是 源來適你 Slack YuniKorn channel 畫板的備份, 最新資訊請參見 Slack)

## 相關資訊
* https://yunikorn.apache.org/community/get_involved/
* https://yunikorn.apache.org/community/how_to_contribute
* 源來適你 YuniKorn 每週五 Syncup 會議 (訂閱`源來適你`的行事曆, 主要聊天, 次要討論問題)

## 新手上路

**Q: 我剛加入, 該從哪裡開始**
1. Create K8S Environment (In kind) 
2. 跑完 [Quick Start](https://yunikorn.apache.org/docs/next/)
3. 讀完 [Contributor Guide](https://yunikorn.apache.org/community/how_to_contribute)
4. [Create Jira account](https://yunikorn.apache.org/community/how_to_contribute#jira-signup)
5. 讀完 [YuniKorn Shim](https://github.com/apache/yunikorn-k8shim) 的 Readme.md
6. 讀完其他子專案的 Readme.md (yunikorn-core,yunikorn-release,yunikorn-web,yunikorn-site)
7. Run E2E Test (In YuniKorn Shim)
8. Build YuniKorn Shim, deploy 到你的 Kind 環境
9. 在 [Core ClusterContext.schedule()](https://github.com/apache/yunikorn-core/blob/87b1d7cf4d083fca75ea389ca025327b18bffd9a/pkg/scheduler/context.go#L119)加上 Log後, 再 Build 一次 YuniKorn 確保 Log 有出現在 Pod Log
10. 用 Rest API 去 Dump YuniKorn status

**Q: 怎麼學 YuniKorn**
1. 閱讀 User Guide, 確保 
    1. 我懂如何[設定 YuniKorn Queue](https://yunikorn.apache.org/docs/next/user_guide/queue_config)
    2. 我懂不同 [Placement Rule](https://yunikorn.apache.org/docs/next/user_guide/placement_rules) 的差異
    3. 我懂 [Gang Scheduling](https://yunikorn.apache.org/docs/next/user_guide/gang_scheduling) 中 TaskGroups 的概念
    4. 我懂 [Preemption](https://yunikorn.apache.org/docs/next/user_guide/preemption_cases) 的七個 Rule
2. 看懂 [Use Cases Document](https://yunikorn.apache.org/docs/next/user_guide/use_cases)
3. [Shim deployments](https://github.com/apache/yunikorn-k8shim/tree/master/deployments) 裡面有一些簡單範例, 可以跑看看
4. 加入 YuniKorn Slack, 回答 User 問題可以刷存在感順便練習
5. Read [E2E tests](https://github.com/apache/yunikorn-k8shim/tree/master/test/e2e) (In YuniKorn Shim), 想理解什麼功能, 就去閱讀對應的 E2E Test 

**Q: 怎麼找 Issue**

1. 高手都直接讀 Code
2. 訂閱 [YuniKorn mail list (dev)](https://issues.apache.org/jira/issues/?jql=project%20%3D%20YUNIKORN%20AND%20labels%20%3D%20newbie%20AND%20resolution%20%3D%20Unresolved%20ORDER%20BY%20created%20DESC%2C%20priority%20DESC%2C%20updated%20DESC)
3. Jira newbie issues ([Link](https://issues.apache.org/jira/issues/?jql=project%20%3D%20YUNIKORN%20AND%20labels%20%3D%20newbie%20AND%20resolution%20%3D%20Unresolved%20ORDER%20BY%20priority%20DESC%2C%20updated%20DESC))
4. 去讀官方文件, 覺得哪裡寫太爛直接改
5. 加入 YuniKorn Slack Channel
6. 參加 YuniKorn Community Syncup, 可以了解未來的新功能, 或者 User 反應的 Bugs
7. 翻閱過往的 Issue List, 超過三個月沒動靜就留言看看
8. Review 別人的 Code , 撿別人的魚尾
9. 看哪裡有缺 Unit Test
10. 沒事跑看看 E2E Test 檢查最新版的 Core 有沒有 Bug
11. 看哪裡不順眼, 就去改 (ex: E2E test 裡面有很多可以重構的點, 改這個風險比較低)

## 進階

**Q: 我要從哪裡開始 Trace Scheduling 邏輯**
- k8shim ->  [pkg/cache/application.go](https://github.com/apache/yunikorn-k8shim/blob/22228dfade3b27909adf62a43fca97f61b2876fe/pkg/cache/application.go#L350) > schedule() 
- Core -> [pkg/scheduler/context.go](https://github.com/apache/yunikorn-core/blob/87b1d7cf4d083fca75ea389ca025327b18bffd9a/pkg/scheduler/context.go#L119) > schedule()
- 搭配[有限狀態機](https://yunikorn.apache.org/docs/next/developer_guide/scheduler_object_states)文件, 看得懂你就是高手

**Q: 測試自己有多了解 YuniKorn**
- 試著回答 Admission Controller 的用途
- 試著回答 Informer 的用途
- 試著回答 YuniKorn Scheduler Interface 的腳色
- 試著回答 Shim 裡面 Dispatcher, Core 裡面 RMProxy 的用途
- 試著回答 Allocation/AllocationAsk 差別 (bound)
- 試著回答 Placeholder 是什麼

## 參考資源

- [Apache YuniKorn 程式碼探討 - K8shim 和 Core 之間的溝通方式（RMProxy）](https://hackmd.io/@vegetableBird/r1wtFbwh0)
- [Apache YuniKorn 程式碼探討 - App State Scheduling](https://hackmd.io/@vegetableBird/Bk9EoCLsA)
- [Apache YuniKorn 程式碼探討 - Gang Scheduling](https://hackmd.io/@vegetableBird/S1tVS52oC)
- [Apache Yunikorn - Preemption](https://light.ryankert.cc/p/apache-yunikorn-%E7%9A%84-preemption-%E6%A9%9F%E5%88%B6/)

## 實際應用情境

1. [執行 Spark on YuniKorn](https://yunikorn.apache.org/docs/next/user_guide/workloads/run_spark)
2. [執行 Flink on YuniKorn](https://yunikorn.apache.org/docs/next/user_guide/workloads/run_flink)
3. [執行 Ray on YuniKorn](https://yunikorn.apache.org/docs/next/user_guide/workloads/run_ray_job)

## Other Tools
- [Go Report Card (yunikorn-core)](https://goreportcard.com/report/github.com/apache/yunikorn-core#misspell)

