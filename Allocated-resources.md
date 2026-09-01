# Allocated resources


```bash
kubectl describe nodes | grep -A10 "Allocated resources"
```

```bash
kubectl get pods -A -o custom-columns='NAMESPACE:.metadata.namespace,POD:.metadata.name,MEMORY:.spec.containers[*].resources.requests.memory,CPU:.spec.containers[*].resources.requests.cpu' | sort -k3 -h
```
