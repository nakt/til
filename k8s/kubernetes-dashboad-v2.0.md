# Kubernetes Dashboard 2.0

## Environment

Kubernetes
```
Client Version: v1.17.3
Server Version: v1.14.9-eks-f459c0
```

## Install

インストール
```
# kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
```

## Configuration

ダッシュボード認証用のサービスアカウントを作成 ([Creating Sample User](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md))

manifest.yaml
```
apiVersion: v1
kind: ServiceAccount
metadata:
    name: admin-user
    namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
    name: admin-user
roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: ClusterRole
    name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

Tokenを取得
```
kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
```

Proxyを有効化
```
# kubectl proxy
```

ブラウザで下記URLにアクセス、Tokenを入力してSign-inする
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

## Refs

- [Kubernetes Dashboard](https://github.com/kubernetes/dashboard)
- [Kubernetes Dashboard 2.0.0 リリース!!](https://shu-mutou.github.io/slideshow.html?md=/slides/kd200ja.md&title=Kubernetes-Dashboard-2.0.0-released&theme=https://shu-mutou.github.io/revealjs-custom-jp.css#/)
