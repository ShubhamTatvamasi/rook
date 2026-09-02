# test

Create test resource:
```bash
kubectl apply -f https://github.com/ShubhamTatvamasi/rook/raw/master/ceph-test.yaml
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

