# rook

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


