# Helm Chart for Kuboard v3

A helm chart for kuboard v3.

Kuboard: [https://kuboard.cn/](https://kuboard.cn/)

**This is NOT an official helm chart, and is in the alpha test version.**
**Use it at your own risk.**

---

## Usage

### TL;DR
```sh
helm repo add mikumikumi https://chart.mikumikumi.xyz/
helm repo update
kubectl create namespace kuboard
helm install kuboard mikumikumi/kuboard -n kuboard \
    --set Ingress.Host=kuboard.example.com
```

### Important Values

See full values at [`values.yaml`](./values.yaml).

- `Config.AgentKey`: **Recommended to manually set.** This is the key used by the Agent to communicate with Kuboard. Please manually specify as any 32-bit string containing letters and numbers. After this key change, the Kuboard Agent needs to be deleted and re imported. Default to `32b7d6572c6255211b4eec9009e4a816`.
- `Storage.EtcdStorageClass`/`Storgae.KuboardStorageClass`: Specify storage class of Etcd and Kuboard. To specify the default storage class, set these keys to `null`. Default to `null`.
- `Ingress.Enable`: Whether enable or disable the ingress. Default to `true`.
- `Ingress.IngressClass`: Specify ingress class of Kuboard. To specify the default ingress class, set it to `null`. Default to `null`.
- `Ingress.Host`: **Manually setting required unless `Ingress.Enable` was set to `false`.** Configure ingress host of Kuboard, for example: `kuboard.example.com`.
- `Service.*`: Configure service ports of Kuboard. To disable NodePort, set `NodePort` to `null`. If NodePorts of agent server are set to 'null', the agent server will not be available unless port-forward configured.
- `Frontend.*`: These values will be used to set up the front-end page. It is helpful when using a reverse proxy.
- `Frontend.Endpoint`: When `Ingress.Enable` is `true`, this key will be automatically set to `https://<Ingress.Host>`(Of course, it can be manually overwritten). When `Ingress.Enable` is `false`, **manually setting this key is required**.
