# Helm基本概念介绍
## Helm 有三个重要的概念需要了解，分別为 Chart、Release 和 Repository，细节如下：
* Chart：主要定义要被执行的应用程式中，所需要的工具、资源、服务等资料，有点类似 Homebrew 的 Formula 或是 APT 的 dpkg 安装包。
* Release：一个被执行于 Kubernetes 的 Chart 实例。Chart 能够在一个集群中拥有多个 Release，例如 MySQL Chart，可以在集群中建立基于该 Chart 的两个资料库实例，其中每个 Release 都会有独立的名称。这个类似docker里面镜像和容器的关系。
* Repository：主要用来存放 Chart 的仓库，如 KubeApps。

## Helm安装chart时配置文件的优先级
通过Values可以获取到的变量有四种类型，变量优先级依次提高
* 文件values.yaml
* 依赖chart中的也可以获取到主chart中的变量
* 命令 helm install 和 helm upgrade 中参数 -f 传递的变量文件中的变量
* 通过 -set 参数传递的键值对变量 优先级最高

# 常用heml命令
## 添加常用的仓库 更新仓库 (前面已介绍)
```
# 阿里提供正式的Charts镜像服务
$ helm repo add apphub https://apphub.aliyuncs.com
# 更新存储库
$ helm repo update
# 看当前的仓库
$ helm repo list
```
## 查询应用
```
$ helm search repo kafka
NAME                     	CHART VERSION	APP VERSION	DESCRIPTION
apphub/kafka             	7.2.2        	2.4.0      	Apache Kafka is a distributed streaming platform.
bitnami/kafka            	10.1.1       	2.4.1      	Apache Kafka is a distributed streaming platform.
```
## 查看安装包的内容
```
$ helm show all apphub/kafka
$ helm show readme apphub/kafka
$ helm show values apphub/kafka  # 查看暴露的自定义参数
$ helm show chart apphub/kafka
```
## 下载某个chart
```
$ helm pull apphub/kafka --untar
# --untar 下载后直接解压
```
## 安装应用
```
$ helm install kafka apphub/kafka -n namespace --dry-run --debug
# -n, --namespace 参数指定安装的命名空间, Helm3 可以在不同的命名空间中部署相同名称的应用
# -f values.yaml 使用自定义参数配置
# --set 设置自定义参数列表
# --dry-run --debug 模拟安装过程并打印配置信息.(并不会实际安装)
```
## 查看状态
```
$ helm status -n namespace kafka
NAME: kafka
LAST DEPLOYED: Fri Apr 10 15:09:42 2020
NAMESPACE: namespace
STATUS: deployed
REVISION: 3
NOTES:
...
```
## 升级应用
```
$ helm upgrade kafka apphub/kafka -n namespace -f values.yaml --dry-run --debug
# --dry-run --debug 模拟安装过程并打印配置信息.(并不会实际安装)
```
## 查看版本记录
```
$ helm history -n namespace kafka
REVISION	UPDATED                 	STATUS    	CHART       	APP VERSION	DESCRIPTION
1       	Thu Apr  9 21:56:30 2020	superseded	kafka-0.20.9	5.0.1      	Install complete
2       	Thu Apr  9 22:00:35 2020	superseded	kafka-0.20.9	5.0.1      	Upgrade complete
3       	Fri Apr 10 15:09:42 2020	deployed  	kafka-0.20.9	5.0.1      	Upgrade complete
```
## 应用回退
```
$ helm rollback -n namespace kafka 2
# 这里的2是上面版本记录中的 REVISION 字段
```
## 查看安装的release
```
$ helm list -n namespace
NAME              	NAMESPACE      	REVISION	UPDATED                             	STATUS  	CHART         	APP VERSION
bot-push          	namespace	2       	2020-04-10 15:10:15.508345 +0800 CST	deployed	test 	1.16.1
comet             	namespace	2       	2020-04-10 15:10:08.668244 +0800 CST	deployed	test 	1.16.1
```
## 查看某个已安装的chart
```
$ helm get all -n namespace kafka
$ helm get manifest -n namespace kafka
---
# 这里的Source表明该配置是根据哪个文件生成的
# Source: kafka/charts/zookeeper/templates/poddisruptionbudget.yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
```
## 卸载应用
```
$ helm uninstall -n namespace kafka
```