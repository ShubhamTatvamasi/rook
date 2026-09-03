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
  generateBucketName: ceph-test-
  storageClassName: ceph-bucket
EOF
```

Check the generated credentials: 
```bash
kubectl get secret ceph-test-bucket -n default -o yaml | \
  yq '.data |= with_entries(.value |= @base64d)'
```
