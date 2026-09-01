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

---

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


