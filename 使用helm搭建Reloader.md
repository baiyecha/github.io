# 使用helm搭建Reloader
## 场景
pod使用的configmap更新后，pod不会重新加载，如果需要重新加载，需要kill pod，使其重启
所以有两个问题：
1. 如何在更新configmap后，将所有使用configmap的pod全部重启启动
2. 如何滚动更新

## 方案
使用工具Reloader
https://github.com/stakater/Reloader
## 简单的使用教程
```
helm repo add stakater https://stakater.github.io/stakater-charts

helm repo update

helm install stakater/reloader --set reloader.watchGlobally=false --namespace test
```
* reloader.watchGlobally  是否监听所有空间

需要自动更新的Deployment添加annotations

```
kind: Deployment
metadata:
  annotations:
    reloader.stakater.com/auto: "true"
spec:
  template: metadata:
```

