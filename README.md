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
  --namespace rook-ceph \
  --force-conflicts \
  --set monitoring.enabled=true \
  --set toolbox.enabled=true \
  --set toolbox.resources=null \
  --set cephClusterSpec.resources.mgr=null \
  --set cephClusterSpec.resources.mon=null \
  --set cephClusterSpec.resources.osd=null \
  --set cephClusterSpec.resources.prepareosd=null \
  --set cephClusterSpec.resources.mgr-sidecar=null \
  --set cephClusterSpec.resources.crashcollector=null \
  --set cephClusterSpec.resources.logcollector=null \
  --set cephClusterSpec.resources.cleanup=null \
  --set cephClusterSpec.resources.exporter=null \
  --set cephClusterSpec.resources.cmd-reporter=null \
  --set cephClusterSpec.dashboard.prometheusEndpoint="http://kube-prometheus-stack-prometheus.monitoring.svc:9090"
```



```
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- \
  ceph dashboard set-grafana-api-url "http://kube-prometheus-stack-grafana.monitoring.svc:80"
```

```
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- \
  ceph dashboard set-grafana-frontend-api-url "http://kube-prometheus-stack-grafana.monitoring.svc:80"
```

