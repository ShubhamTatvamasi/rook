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

Install `rook-ceph`:
```bash
helm upgrade -i rook-ceph rook/rook-ceph 
  --create-namespace \
  --namespace rook-ceph
```

Install `rook-ceph-cluster`:
```bash
helm upgrade -i rook-ceph-cluster rook/rook-ceph-cluster 
  --create-namespace \
  --namespace rook-ceph
```

---

Access Dashboard:
```bash
kubectl -n rook-ceph port-forward svc/rook-ceph-mgr-dashboard 8443:8443
```

dashboard password:
```bash
kubectl -n rook-ceph get secret rook-ceph-dashboard-password \
  -o jsonpath='{.data.password}' | base64 -d; echo
```

