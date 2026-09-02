# test

Create test resource:
```bash
kubectl apply -f https://github.com/ShubhamTatvamasi/rook/raw/master/ceph-test.yaml
```

Tail the logs:
```bash
kubectl -n default logs ceph-test-pod -f
```

Get inside pod:
```bash
kubectl -n default exec -it ceph-test-pod -- sh
```


Delete test resources:
```bash
kubectl delete -f https://github.com/ShubhamTatvamasi/rook/raw/master/ceph-test.yaml
```

