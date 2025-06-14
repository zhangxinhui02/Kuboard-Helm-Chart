# Kuboard Helm Chart

A helm chart for kuboard v3.

[中文](./README.md) | [English](./README_en.md)

Kuboard: [Official Site](https://kuboard.cn/) | [Official Install Guide](https://kuboard.cn/install/v3/install-in-k8s.html#%E6%96%B9%E6%B3%95%E4%BA%8C-%E4%BD%BF%E7%94%A8-storageclass-%E6%8F%90%E4%BE%9B%E6%8C%81%E4%B9%85%E5%8C%96)

*There are several errors in the official yaml file provided by Kuboard. This chart has corrected these errors.*

**This is NOT an official helm chart. Use it at your own risk.**

---

## Usage

You can install any number of releases to any namespace, as long as the release name is different.

The OCI Artifacts address for this helm chart is: `oci://reg.mikumikumi.xyz/charts/kuboard`

[Learn about the usage of Helm's OCI-based registries](https://helm.sh/docs/topics/registries/)

### TL;DR
```sh
kubectl create namespace kuboard

helm install -n kuboard kuboard oci://reg.mikumikumi.xyz/charts/kuboard \
    --set Ingress.Host=kuboard.example.com
```

### Important Values

See all values in [`values.yaml`](./values.yaml).

- `Config.AgentKey`: **Recommended to manually set.** This is the key used by the Agent to communicate with Kuboard. Please manually specify as any 32-bit string containing letters and numbers. After this key change, the Kuboard Agent needs to be deleted and re imported. Default to `32b7d6572c6255211b4eec9009e4a816`.


- `Storage.EtcdStorageClass`/`Storgae.KuboardStorageClass`: Specify storage class of Etcd and Kuboard. To specify the default storage class, set these keys to `null`. Default to `null`.


- `Ingress.Enable`: Whether enable or disable the ingress. Default to `true`.
- `Ingress.IngressClass`: Specify ingress class of Kuboard. To specify the default ingress class, set it to `null`. Default to `null`.
- `Ingress.Host`: **Manually setting required unless `Ingress.Enable` was set to `false`.** Configure ingress host of Kuboard, for example: `kuboard.example.com`.


- `Service.*`: Configure service ports of Kuboard. To disable NodePort, set `NodePort` to `null`. If NodePorts of agent server are set to `null`, the agent server will not be available unless port-forward configured by user.


- `Frontend.*`: These values will be used to set up the front-end page. It is helpful when using a reverse proxy.
- `Frontend.Endpoint`: Entry point of frontend page. When `Ingress.Enable` is `true`, this key will be automatically set to `https://<Ingress.Host>`(Of course, it can be manually overwritten). When `Ingress.Enable` is `false`, **manually setting is required**.
- `Frontend.AgentServerTcp`/`Frontend.AgentServerUdp`: These keys will be automatically set to `'Service.*.NodePort'`(Of course, it can be manually overwritten).

When running Kuboard on a slow network/file system, timeout errors may occur. In this case, please refer to field `NodeSelectorHostname` and `Etcd` in `values.yaml`.

## Change Logs

- 0.1.6: Change the accessMode of Etcd's PVC to `ReadWriteOnce`.
- 0.1.5: Change the scheme for specifying node from `nodeName` to `nodeSelector.kubernete.io/hostname` selector.
- 0.1.4: Modified the storage location and deployment method of the chart. Fixed the incorrect referencing of PVC in Etcd StatefulSet.
- 0.1.3: Change default kuboard image to newest 3.5.2.7. Wrong nodeName template of Etcd StatefulSet was fixed.
- 0.1.2: Kuboard can be specified to run on specific nodes to avoid timeout error. The `heartbeat-interval` and `election-timeout` parameters of Etcd can be manually set.
- 0.1.1: Multiple releases in one namespace are supported. You can install any number of releases to any namespace, as long as the release name is different.
- 0.1.0: The initial chart. Can be installed in a separate namespace.
