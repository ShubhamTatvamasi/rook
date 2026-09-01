# dashboard

Setup Dashboard Proxy:
```
kubectl -n rook-ceph run dashboard-proxy \
  --image=alpine/socat \
  --restart=Never \
  -- -d -d TCP-LISTEN:8443,fork,reuseaddr TCP:rook-ceph-mgr-dashboard:8443
```

Access Dashboard:
```bash
kubectl -n rook-ceph port-forward pod/dashboard-proxy 8443:8443
```

https://localhost:8443

dashboard password:
```bash
kubectl -n rook-ceph get secret rook-ceph-dashboard-password \
  -o jsonpath='{.data.password}' | base64 -d; echo
```

Cleanup:
```bash
kubectl -n rook-ceph delete pod dashboard-proxy
```
