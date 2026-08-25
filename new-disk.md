# New Disk

You can force the operator to reconcile by restarting it:
```bash
kubectl -n rook-ceph rollout restart deployment rook-ceph-operator
```

Then watch:
```
kubectl -n rook-ceph logs -f deploy/rook-ceph-operator
```
