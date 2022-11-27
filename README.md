# rook

https://rook.io/docs/rook/v1.10/Getting-Started/quickstart/

Clone the latest rook repo and deploy rook:
```bash
git clone --single-branch --branch v1.10.6 https://github.com/rook/rook.git
cd rook/deploy/examples
kubectl create -f crds.yaml -f common.yaml -f operator.yaml
kubectl create -f cluster.yaml
```
