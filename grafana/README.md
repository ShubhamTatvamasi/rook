# Grafana

https://github.com/ceph/ceph/tree/main/monitoring/ceph-mixin/dashboards_out

Everything here runs over plain HTTP. The Ceph dashboard is deployed with `dashboard.ssl=false` (see root [README.md](../README.md)) specifically so it can embed Grafana's panels without hitting a browser mixed-content block — keep Grafana on HTTP too rather than adding TLS to one side only.

Install `kube-prometheus-stack` (Prometheus + Grafana + Alertmanager):
```bash
helm repo update prometheus

helm install kube-prometheus-stack prometheus/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace \
  --set prometheus.prometheusSpec.serviceMonitorSelectorNilUsesHelmValues=false \
  --set prometheus.prometheusSpec.podMonitorSelectorNilUsesHelmValues=false
```

`serviceMonitorSelectorNilUsesHelmValues=false` and `podMonitorSelectorNilUsesHelmValues=false` are required so Prometheus scrapes ServiceMonitors cluster-wide, not just ones carrying this chart's own release label (Rook's ServiceMonitor doesn't carry it).

---

Enable Grafana dashboard embedding + anonymous viewer access:
```bash
helm upgrade kube-prometheus-stack prometheus/kube-prometheus-stack \
  --namespace monitoring \
  --reuse-values \
  -f grafana-embedding-values.yaml
```

`grafana-embedding-values.yaml`:
```yaml
grafana:
  grafana.ini:
    auth.anonymous:
      enabled: true
      org_role: Viewer
    security:
      allow_embedding: true

  # Ceph's dashboard module hardcodes its Grafana panel URLs with
  # var-datasource=Dashboard1, so a datasource literally named
  # "Dashboard1" is required alongside the default "Prometheus" one.
  additionalDataSources:
    - name: Dashboard1
      type: prometheus
      access: proxy
      url: http://kube-prometheus-stack-prometheus.monitoring.svc:9090
      isDefault: false
      editable: false
```

---

**Required** — grant the Rook mgr RBAC to create/manage ServiceMonitors in the `rook-ceph` namespace. Without this, Prometheus has no ServiceMonitor and scrapes nothing, so every Ceph panel in the dashboard (Cluster Utilization included) stays blank regardless of the Grafana URL/embedding settings below. The official chart only creates this RBAC when the operator and cluster live in different namespaces (`templates/rbac.yaml` is gated on `Release.Namespace != operatorNamespace`) — since ours are both `rook-ceph`, apply it manually:
```bash
kubectl apply -f monitoring-rbac.yaml
```

Check the ServiceMonitor appears (may take up to a minute; force it sooner with a mgr restart):
```bash
kubectl get servicemonitor -n rook-ceph

kubectl rollout restart deploy/rook-ceph-mgr-a -n rook-ceph
kubectl rollout restart deploy/rook-ceph-mgr-b -n rook-ceph
```

---

Grafana has no external exposure by default (`ClusterIP` only) — the browser can't load embedded panels against cluster-internal DNS, only the Ceph mgr backend can reach that. Expose Grafana with its own NodePort:
```bash
kubectl apply -f - << EOF
apiVersion: v1
kind: Service
metadata:
  name: kube-prometheus-stack-grafana-nodeport
  namespace: monitoring
  labels:
    app.kubernetes.io/name: grafana
    app.kubernetes.io/instance: kube-prometheus-stack
spec:
  type: NodePort
  ports:
    - name: http-grafana
      port: 80
      targetPort: 3000
      nodePort: 30300
  selector:
    app.kubernetes.io/name: grafana
    app.kubernetes.io/instance: kube-prometheus-stack
EOF
```

Point the Ceph dashboard at Grafana — **backend** (`grafana-api-url`, used server-side by the mgr for the panel-existence check) stays on the fast internal Service DNS; **frontend** (`grafana-frontend-api-url`, the URL your browser loads the iframe from) must be the externally-reachable NodePort, or you'll hit `Firefox/Chrome can't connect to the server at kube-prometheus-stack-grafana.monitoring.svc`:
```bash
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- \
  ceph dashboard set-grafana-api-url "http://kube-prometheus-stack-grafana.monitoring.svc:80"

kubectl -n rook-ceph exec deploy/rook-ceph-tools -- \
  ceph dashboard set-grafana-frontend-api-url "http://10.10.153.255:30300"
```

Verify:
```bash
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- ceph dashboard get-grafana-api-url
kubectl -n rook-ceph exec deploy/rook-ceph-tools -- ceph dashboard get-grafana-frontend-api-url
```

---

Load Ceph's own dashboards into Grafana as ConfigMaps labeled `grafana_dashboard: "1"` — the Grafana sidecar (enabled by default in `kube-prometheus-stack`) auto-discovers and provisions them within about a minute, no restart needed:
```bash
kubectl apply -f dashboards/
```

Watch the sidecar pick them up:
```bash
kubectl logs -n monitoring deploy/kube-prometheus-stack-grafana -c grafana-sc-dashboard --tail=30 -f
```

**Use Ceph's own dashboard JSONs, not the grafana.com community ones.** Ceph's dashboard backend checks a panel exists by querying Grafana for a *specific, hardcoded UID* per page (e.g. the Pool page requests UID `-xyV8KCiz` — visible in a blocked/failed request as `.../d/-xyV8KCiz/ceph-pool-detail?...`), and shows "Grafana Dashboard doesn't exist" if that UID isn't registered. The community dashboards on grafana.com (IDs 2842/5336/5342) use *different* UIDs and will fail this check even though they render the same-looking charts standalone. `dashboards/` here is pulled straight from [`ceph/ceph` monitoring/ceph-mixin/dashboards_out](https://github.com/ceph/ceph/tree/main/monitoring/ceph-mixin/dashboards_out), which is the actual set the dashboard module's UID checks expect — one ConfigMap per file, covering cluster, hosts, OSDs, pools, RGW, RBD, and CephFS pages.

To refresh these from upstream later:
```bash
for f in ceph-cluster-advanced ceph-cluster hosts-overview host-details osds-overview \
         osd-device-details pool-detail pool-overview radosgw-overview radosgw-detail \
         rbd-overview rbd-details cephfsdashboard cephfs-subvolume; do
  curl -sSL "https://raw.githubusercontent.com/ceph/ceph/main/monitoring/ceph-mixin/dashboards_out/${f}.json" -o "${f}.json"
  kubectl create configmap "grafana-dashboard-${f}" \
    --namespace monitoring \
    --from-file="${f}.json=${f}.json" \
    --dry-run=client -o yaml \
  | kubectl label -f - --local -o yaml "grafana_dashboard=1" > "dashboards/cm-${f}.yaml"
done
kubectl apply -f dashboards/
```
