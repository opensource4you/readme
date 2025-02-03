# KubeRay

## 相關資訊

* [KubeRay project tracker](https://docs.google.com/document/d/1Q78Ny7KpTVOleB51-Tb1fewIDMm9msCTG4kDdWonwiQ/edit)
* 如果需要權限請找陳楷訓 

## 注意事項

* 問問題盡量在 channel 裡面問，不要私訊，避免 mentor 需要重複回答類似的問題
* Pull request template 裡面的 `Closes #1234`，那個 `Closes` 是有意義的，會把 PR 和對應的 issue link 起來，讓 PR 被 merge 之後 issue 會自動關掉，請不要自己省略掉。

## FAQ

* 想貢獻 [Ray](https://github.com/ray-project/ray) 可以嗎？
  * 可以，但是 Ray 的 PR 目前 mentor 們沒有 merge 的權限，而 KubeRay 楷訓有 merge 的權限，比較好帶人，故以 KubeRay 為主。如果想貢獻 Ray 的話，發了 PR 之後可以跟楷訓講，他會找人幫你 review。

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
* 看一下怎麼 build Ray 的 documentation（doc 修改的 PR 是發在 ray 的 repo，不是 kuberay）
  * https://docs.ray.io/en/latest/ray-contribute/docs.html
  * 請用 `conda` 不要用 `venv`
  * 請用 `make develop` 不要用 `make local`
  * Build 完之後會有一個 `_build` 的資料夾，執行 `python -m http.server --directory _build/html` 然後就可以去 `localhost:8000` 看到 doc

## Ray 新手上路


* 找一台比較好的機器，不然要 build 很久
* 照著[這個 doc](https://docs.ray.io/en/master/ray-contribute/development.html#preparing-to-build-ray-on-linux) 裝一下 dependencies
* [Additional dependencies](https://docs.ray.io/en/master/ray-contribute/development.html#installing-additional-dependencies-for-development) 順便也裝一下
* 裝 [pre-commit hook](https://docs.ray.io/en/master/ray-contribute/development.html#pre-commit-hooks)
* 照著[這個 doc](https://docs.ray.io/en/master/ray-contribute/development.html#building-ray-on-linux-macos-full) build 一次完整的 Ray
* 只有第一次 build 的時候需要用 `pip install` 的方式 build，之後都是用 `bazel build -c fastbuild //:ray_pkg` 即可
  * https://docs.ray.io/en/master/ray-contribute/development.html#fast-debug-and-optimized-builds
* 一些比較常用的指令可以參考[奇聖的 Ray 指令 cheatsheet](https://github.com/MortalHappiness/dotfiles/blob/main/dot_local/share/navi/cheats/exact_personal-cheatsheets/projects/ray.cheat)
