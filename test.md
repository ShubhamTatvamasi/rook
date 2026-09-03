# test

We need 4 Nodes so we can delete 1 node so we still have 3 nodes quorum

Create test resources with `ceph-block` storage class:
```bash
kubectl apply -f https://github.com/ShubhamTatvamasi/rook/raw/master/ceph-test.yaml
```

Create test resources with `ceph-filesystem` storage class:
```bash
curl -sL https://github.com/ShubhamTatvamasi/rook/raw/master/ceph-test.yaml \
  | sed 's/ceph-block/ceph-filesystem/g' \
  | kubectl apply -f -
```

Tail the logs:
```bash
kubectl -n default logs ceph-test-pod -f
```

Check file size:
```bash
watch -n 1 kubectl -n default exec ceph-test-pod -- ls -lh /data/timestamps.log
```


Delete test resources:
```bash
kubectl delete -f https://github.com/ShubhamTatvamasi/rook/raw/master/ceph-test.yaml
```

