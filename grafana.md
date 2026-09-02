# grafana

https://github.com/ceph/ceph/tree/main/monitoring/ceph-mixin/dashboards_out

Internal URL
```
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- \
  ceph dashboard set-grafana-api-url "http://kube-prometheus-stack-grafana.monitoring.svc:80"
```

Public URL
```bash
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- \
  ceph dashboard set-grafana-frontend-api-url "https://grafana.k8s.shubhamtatvamasi.com"
```
