# KubeRay

## 相關資訊

* [KubeRay project tracker](https://docs.google.com/document/d/1Q78Ny7KpTVOleB51-Tb1fewIDMm9msCTG4kDdWonwiQ/edit)
* 如果需要權限請找 mentors

## 注意事項

* 加進 slack channel 之後，先看上面的 Canvas
  <img width="1062" height="172" alt="image" src="https://github.com/user-attachments/assets/515e6db8-2e29-4635-a01b-dcd0ab7e87a5" />
* 問問題盡量在 channel 裡面問，不要私訊，避免 mentor 需要重複回答類似的問題
* Pull request template 裡面的 `Closes #1234`，那個 `Closes` 是有意義的，會把 PR 和對應的 issue link 起來，讓 PR 被 merge 之後 issue 會自動關掉，請不要自己省略掉。
* 建議閱讀以下文章建立正確的開源貢獻心態，希望大家可以正確的使用 AI 而不是造成社群的困擾:
    * [參與開源社群應有的心態](../articles/mindset-community-etiquette/README.md)
    * [使用 AI 工具時如何維持貢獻品質](../articles/mindset-ai-assisted-contribution/README.md)

## FAQ

* 想貢獻 [Ray](https://github.com/ray-project/ray) 可以嗎？
  * 可以，Ray 的部分會主要由 [林宥呈] (Ray Data) 、[劉奇聖] (Ray Core) 和 [Rueian] (Ray Core) 負責，偶爾可能會有 issues / 大鍋菜可以做。

* 發 PR 後要找誰 review？
  * 我們鼓勵各位小夥伴互相 review PR，有 PR 的話建議可以先丟到群組 (KubeRay PR 可以丟到 Ray Slack 裡面的
  `kuberay-dev` 群組增加可見度) 讓其他小夥伴幫忙 review，有小夥伴 approve 後再 ping mentors review。

## KubeRay 新手上路

* 如果是超級開源新手，不熟悉正常開源專案怎麼發 PR、怎麼 sync upstream 之類的，請先看[開源貢獻新手指南](https://chishengliu.com/zh-tw/series/%E9%96%8B%E6%BA%90%E8%B2%A2%E7%8D%BB%E6%96%B0%E6%89%8B%E6%8C%87%E5%8D%97/)
* 把 [ray](https://github.com/ray-project/ray) 和 [kuberay](https://github.com/ray-project/kuberay) 這兩個 repo star、fork、clone，並執行完上面那篇文章的初始流程
* 如果不熟悉 Kubernetes，請自己找教學稍微學一下，網路上面資源很多，至少需要懂基本的 Pod、Deployment、Service 之類的
* 如果不熟悉 Kubernetes Operator，以下是一些閱讀資源
  * [Kubernetes Operator (Controller) 教學系列文（寫到一半）](https://chishengliu.com/zh-tw/series/kubernetes-operator-controller-%E6%95%99%E5%AD%B8/)
  * https://cloudark.medium.com/kubernetes-custom-controllers-b6c7d0668fdf
  * https://www.linkedin.com/pulse/kubernetes-custom-controllers-part-1-kritik-sachdeva/
  * https://www.linkedin.com/pulse/kubernetes-custom-controller-part-2-kritik-sachdeva/
  * https://blog.csdn.net/yanchendage/article/details/134310876
* 試著 compile 並執行 unit tests 和 e2e tests，`pre-commit` hook 也記得裝一下
  * https://github.com/ray-project/kuberay/blob/master/ray-operator/DEVELOPMENT.md
* 試著跑 RayCluster、RayJob、RayService
  * https://docs.ray.io/en/latest/cluster/kubernetes/getting-started.html
* (Optional) 看一下怎麼 build Ray 的 documentation（doc 修改的 PR 是發在 ray 的 repo，不是 kuberay）
  * https://docs.ray.io/en/latest/ray-contribute/docs.html
  * 但其實這步可以略過，因為你可以直接發 draft PR 然後 CI 就會幫你 build 出一個 doc link 了，平常開發其實很少會需要在 local machine build docs

## Ray 新手上路


* 找一台比較好的機器，不然要 build 很久
* 照著[這個 doc](https://docs.ray.io/en/master/ray-contribute/development.html#preparing-to-build-ray-on-linux) 裝一下 dependencies
* [Additional dependencies](https://docs.ray.io/en/master/ray-contribute/development.html#installing-additional-dependencies-for-development) 順便也裝一下
* 裝 [pre-commit hook](https://docs.ray.io/en/master/ray-contribute/development.html#pre-commit-hooks)
* 照著[這個 doc](https://docs.ray.io/en/master/ray-contribute/development.html#building-ray-on-linux-macos-full) build 一次完整的 Ray
* 只有第一次 build 的時候需要用 `pip install` 的方式 build，之後都是用 `bazel build -c fastbuild //:ray_pkg` 即可
  * https://docs.ray.io/en/master/ray-contribute/development.html#fast-debug-and-optimized-builds
* [其他雜記](https://chishengliu.notion.site/Ray-17e6c9db49d480ffb47af4a72d2a3564?pvs=4)

<!-- Reference Links -->
[林宥呈]: https://opensource4you.slack.com/team/U085SQF5GF3
[Rueian]: https://opensource4you.slack.com/team/U056T5Y0QLB
[劉奇聖]: https://opensource4you.slack.com/team/U06CSLLGQNR
