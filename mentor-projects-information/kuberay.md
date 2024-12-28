# KubeRay

## 相關資訊

* [KubeRay project tracker](https://docs.google.com/document/d/1Q78Ny7KpTVOleB51-Tb1fewIDMm9msCTG4kDdWonwiQ/edit)
* 如果需要權限請找陳楷訓 

## 新手上路

* 如果是超級開源新手，不熟悉正常開源專案怎麼發 PR、怎麼 sync upstream 之類的，請先看[開源貢獻新手指南](https://chishengliu.com/zh-tw/series/%E9%96%8B%E6%BA%90%E8%B2%A2%E7%8D%BB%E6%96%B0%E6%89%8B%E6%8C%87%E5%8D%97/)
* 把 [ray](https://github.com/ray-project/ray) 和 [kuberay](https://github.com/ray-project/kuberay) 這兩個 repo star、fork、clone，並執行完上面那篇文章的初始流程
* 如果不熟悉 Kubernetes，請自己找教學稍微學一下，網路上面資源很多，至少需要懂基本的 Pod、Deployment、Service 之類的
* 如果不熟悉 Kubernetes Operator，以下是一些閱讀資源
  * [Kubernetes Operator (Controller) 教學系列文（寫到一半）](https://chishengliu.com/zh-tw/series/kubernetes-operator-controller-%E6%95%99%E5%AD%B8/)
  * https://cloudark.medium.com/kubernetes-custom-controllers-b6c7d0668fdf
  * https://www.linkedin.com/pulse/kubernetes-custom-controllers-part-1-kritik-sachdeva/
  * https://www.linkedin.com/pulse/kubernetes-custom-controller-part-2-kritik-sachdeva/
  * https://blog.csdn.net/yanchendage/article/details/134310876
* 試著 compile 並執行 unit tests 和 e2e tests
  * https://github.com/ray-project/kuberay/blob/master/ray-operator/DEVELOPMENT.md
* 試著跑 RayCluster 和 RayJob
  * https://docs.ray.io/en/latest/cluster/kubernetes/getting-started.html
* 看一下怎麼 build Ray 的 documentation
  * https://docs.ray.io/en/latest/ray-contribute/docs.html
  * 請用 `conda` 不要用 `venv`
  * 請用 `make develop` 不要用 `make local`
  * Build 完之後會有一個 `_build` 的資料夾，執行 `python -m http.server --directory _build/html` 然後就可以去 `localhost:8000` 看到 doc

## 注意事項

* 問問題盡量在 channel 裡面問，不要私訊，避免 mentor 需要重複回答類似的問題

## FAQ

* 想貢獻 [Ray](https://github.com/ray-project/ray) 可以嗎？
  * 可以，但是 Ray mentor 們沒有 merge 的權限，而 KubeRay 楷訓有 merge 的權限，比較好帶人，故以 KubeRay 為主。如果想貢獻 Ray 的話，發了 PR 之後可以跟楷訓講，他會找人幫你 review。