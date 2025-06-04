# Kuboard Helm Chart

一个用于安装Kuboard v3的Helm Chart。

[中文](./README.md) | [English](./README_en.md)

Kuboard: [官方网站](https://kuboard.cn/) | [官方安装指南](https://kuboard.cn/install/v3/install-in-k8s.html#%E6%96%B9%E6%B3%95%E4%BA%8C-%E4%BD%BF%E7%94%A8-storageclass-%E6%8F%90%E4%BE%9B%E6%8C%81%E4%B9%85%E5%8C%96)

*Kuboard官方提供的yaml文件中有几个错误。此Chart已更正了这些错误。*

**这不是官方Chart。使用时风险自负。**

---

## 使用方法

你可以安装任意数量的Release到任何Namespace，只要Release名称不同即可。

此helm chart的OCI Artifacts地址为：`oci://reg.mikumikumi.xyz/charts/kuboard`

[了解Helm基于OCI的注册中心的使用方法](https://helm.sh/zh/docs/topics/registries/)

### TL;DR
```sh
kubectl create namespace kuboard

helm install -n kuboard kuboard oci://reg.mikumikumi.xyz/charts/kuboard \
    --set Ingress.Host=kuboard.example.com
```

### 重要的Values

在[`values.yaml`](./values.yaml)查看所有可配置的Values。

- `Config.AgentKey`: **建议自行配置。** 这是Agent与Kuboard通信时的密钥。请修改为一个任意的包含字母、数字的32位字符串。此密钥变更后，需要删除Kuboard Agent重新导入。默认值为`32b7d6572c6255211b4eec9009e4a816`。


- `Storage.EtcdStorageClass`/`Storgae.KuboardStorageClass`: 指定Etcd和Kuboard使用的存储类。设置为`null`代表默认存储类。默认值为`null`。


- `Ingress.Enable`: 是否启用Ingress。默认值为`true`。
- `Ingress.IngressClass`: 指定Kuboard使用的存储类。设置为`null`代表默认Ingress Class。默认值为`null`。
- `Ingress.Host`: **需要手动配置，除非`Ingress.Enable`的值为`false`。** 配置Kuboard的Ingress Host，例如`kuboard.example.com`。


- `Service.*`: 配置Kuboard Service的各个端口。若想禁用NodePort，可以将`NodePort`配置为`null`。如果Agent Server的NodePort被配置为`null`，则Agent Server将不可用，除非自行配置了端口转发。


- `Frontend.*`: 这些值被用于配置前端页面。这在使用反向代理时会有帮助。
- `Frontend.Endpoint`: 前端页面的入口。当`Ingress.Enable`被设置为`true`时，此值将被自动设置为`https://<Ingress.Host>`(当然，它可以被手动覆写)。当`Ingress.Enable`被设置为`false`时，**需要手动配置此值**。
- `Frontend.AgentServerTcp`/`Frontend.AgentServerUdp`: 这些值将被自动设置为`'Service.*.NodePort'`(当然，它们可以被手动覆盖)。

在慢速网络/文件系统上运行Kuboard时，可能会发生超时错误。这种情况下请参考`values.yaml`中的`NodeSelectorHostname`和`Etcd`字段。

##  更新日志

- 0.1.5: 将指定节点的方案从`nodeName`修改为`nodeSelector.kubernetes.io/hostname`选择器。
- 0.1.4: 修改了chart存储位置和部署方式。修复了Etcd StatefulSet的pvc的错误引用。
- 0.1.3: 切换默认Kuboard镜像为最新版`3.5.2.7`。修复了Etcd StatefulSet中错误的nodeName模板。
- 0.1.2: 可以指定Kuboard在特定节点上运行，以避免超时错误。Etcd的`heartbeat-interval`和`election-timeout`参数可以手动设置。
- 0.1.1: 支持单个Namespace中的多Release。你可以安装任意数量的Release到任何Namespace，只要Release名称不同即可。
- 0.1.0: 初始的Chart。可以被安装到独立的Namespace中。
