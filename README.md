# rook

| Node      | Role                    | vCPU |   RAM | Root Disk | Ceph Disk |
| --------- | ----------------------- | ---: | ----: | --------: | --------: |
| `rke2-01` | RKE2 Server + Rook/Ceph |    8 | 16 GB |     40 GB |     50 GB |
| `rke2-02` | RKE2 Server + Rook/Ceph |    8 | 16 GB |     40 GB |     50 GB |
| `rke2-03` | RKE2 Server + Rook/Ceph |    8 | 16 GB |     40 GB |     50 GB |

---

Add helm repo:
```bash
helm repo add rook https://charts.rook.io/release
```

Look for latest release:
```bash
helm search repo rook
```

Install `rook-ceph` Operator:
```bash
helm upgrade -i rook-ceph rook/rook-ceph \
  --create-namespace \
  --namespace rook-ceph
```

Install `rook-ceph-cluster`:
```bash
helm upgrade -i rook-ceph-cluster rook/rook-ceph-cluster \
  --namespace rook-ceph
```

---

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
