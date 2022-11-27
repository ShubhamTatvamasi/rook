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

Create Storage Class for (rook-ceph-block) **ReadWriteOnce**:
```bash
kubectl create -f csi/rbd/storageclass.yaml
```

Create Ceph Filesystem for (rook-cephfs) **ReadWriteMany**:
```bash
kubectl create -f filesystem.yaml
kubectl create -f csi/cephfs/storageclass.yaml
```

Check Storage Class:
```bash
kubectl get storageclass
```

Set rook-ceph-block as default Storage Class:
```bash
kubectl patch storageclass rook-ceph-block \
  --patch='{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

Create a pvc and pod:
```bash
curl -sL https://k8s.io/examples/pods/storage/pv-claim.yaml \
  | sed 's/manual/rook-ceph-block/' | kubectl apply -f -

kubectl apply -f https://k8s.io/examples/pods/storage/pv-pod.yaml
```

### Ceph

Check cluster status:
```bash
ceph status
```

```bash
ceph osd status
```
