# Ceph

Get inside `rook-ceph-tools` pod:
```bash
kubectl -n rook-ceph exec -it deploy/rook-ceph-tools -- bash
```

k8s commands:
```bash
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- ceph status
```

Check disks:
```bash
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- ceph osd status
```
