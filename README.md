# rook

https://rook.io/docs/rook/v1.10/Getting-Started/quickstart/

We need attached volumes or hard disks to the worker nodes for ceph to provision.

Clone the latest rook repo and deploy rook:
```bash
git clone --single-branch --branch v1.10.6 https://github.com/rook/rook.git
cd rook/deploy/examples
kubectl create -f crds.yaml -f common.yaml -f operator.yaml
kubectl create -f cluster.yaml
```

Create ROOK toolbox:
```bash
kubectl create -f toolbox.yaml
```

### Ceph

Check cluster status:
```bash
ceph status
```

```bash
ceph osd status
```
