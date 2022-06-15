```sh
# クラスターを作成
$ kind create cluster --config ./kind.yaml

# Ingress NGINX をインストール
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
$ kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s

# Argo CD をインストール
$ kubectl create namespace argocd
$ helm repo add argo https://argoproj.github.io/argo-helm
$ helm install argocd argo/argo-cd --namespace argocd

# Argo CD UI にアクセス
# http://localhost:8080 からアクセスできる
$ kubectl port-forward -n argocd svc/argocd-server 8080:443

# 管理者アカウントの初期パスワードを取得
$ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d

# ログイン
$ argocd login localhost:8080

# パスワード更新
$ argocd account update-password

# 初期パスワードが入ってる Secret を削除
$ kubectl -n argocd delete secrets/argocd-initial-admin-secret

# アプリケーションを作成
# 暫く待つと http://localhost から Wordpress にアクセスできる
$ helm install -n argocd wordpress-cd ./wordpress-cd

# 後片付け
$ kind delete cluster
```

# 参考

- https://argo-cd.readthedocs.io/en/stable/getting_started/
- https://github.com/argoproj/argocd-example-apps
