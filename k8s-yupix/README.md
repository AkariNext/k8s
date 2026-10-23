# k8s-yupix

yupix の自宅クラスター（Proxmox 上の VM 3 台）を Argo CD で管理するための設定です。

| 項目 | 内容 |
|---|---|
| Kubernetes | 1.36（kubeadm） |
| コンテナランタイム | CRI-O 1.36 |
| CNI | Cilium 1.20（kube-proxy を置き換え） |
| LoadBalancer | MetalLB（BGP、`10.255.1.0/24`） |
| ストレージ | NFS（`nfs-client` を既定の StorageClass にする） |
| 秘密情報 | Sealed Secrets |

## 構成

- `root.yaml`: `apps/` の Application をまとめて作る親
- `apps/`: アプリごとの Argo CD の Application
- それ以外のフォルダ: Application が読むマニフェスト

| アプリ | 公開先 |
|---|---|
| openspeedtest | `10.255.1.0:3000` |
| netdata | `10.255.1.2:19999` |
| uptime-kuma | `10.255.1.3:3001` |
| Argo CD | `10.255.1.4`（LAN と Tailscale からのみ） |

## 始め方

Kubernetes と Argo CD を入れたあと、親の Application を一度だけ作ります。

```sh
kubectl apply -f https://raw.githubusercontent.com/AkariNext/k8s/main/k8s-yupix/root.yaml
```

BGP のピアの設定は `metallb/README.md` を参照してください。
