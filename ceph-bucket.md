# ceph-bucket

Create an `ObjectBucketClaim`:
```yaml
kubectl apply -f - << EOF
apiVersion: objectbucket.io/v1alpha1
kind: ObjectBucketClaim
metadata:
  name: ceph-test-bucket
  namespace: default
spec:
  generateBucketName: ceph-test-bucket
  storageClassName: ceph-bucket
EOF
```

Get the list of all `ObjectBucketClaim` created:
```bash
kubectl get ObjectBucketClaim -A
```

Check the generated credentials: 
```bash
kubectl get secret ceph-test-bucket -n default -o yaml | \
  yq '.data |= with_entries(.value |= @base64d)'
```

Check config map for bucket details:
```bash
kubectl get configmap ceph-test-bucket -n default -o yaml
```

Create a pod for testing the bucket:
```bash
kubectl apply -f https://github.com/ShubhamTatvamasi/rook/raw/master/s3-test.yaml
```

---

Cleanup:
```
kubectl delete -f https://github.com/ShubhamTatvamasi/rook/raw/master/s3-test.yaml
kubectl delete -n default ObjectBucketClaim ceph-test-bucket
```

