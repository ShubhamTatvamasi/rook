# Ceph


Check cluster status:
```bash
ceph status
```

```bash
ceph osd status
```

---

k8s commands:
```bash
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- ceph status
```

Check disks:
```bash
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- ceph osd status
```
