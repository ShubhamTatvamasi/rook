# dashboard

http://10.10.153.255:30443

Deploy `rook-ceph-mgr-dashboard-nodeport` service:
```yaml
kubectl apply -f - << EOF
apiVersion: v1
kind: Service
metadata:
  name: rook-ceph-mgr-dashboard-nodeport
  namespace: rook-ceph
  labels:
    app: rook-ceph-mgr
    rook_cluster: rook-ceph
spec:
  type: NodePort
  ports:
    - name: https-dashboard
      port: 7000
      targetPort: 7000
      nodePort: 30080
  selector:
    app: rook-ceph-mgr
    mgr_role: active
    rook_cluster: rook-ceph
EOF
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
