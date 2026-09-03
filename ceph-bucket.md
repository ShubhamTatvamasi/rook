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

Check config map for bucket details:
```bash
kubectl get configmap ceph-test-bucket -n default -o yaml
```

Create a pod for testing the bucket:
```yaml
kubectl apply -f - << EOF
apiVersion: v1
kind: Pod
metadata:
  name: s3-test
  namespace: default
spec:
  restartPolicy: Never

  containers:
    - name: aws
      image: amazon/aws-cli

      command:
        - /bin/sh
        - -c
        - |
          set -e

          mkdir -p ~/.aws

          cat > ~/.aws/config <<EOF
          [default]
          region = ${BUCKET_REGION:-us-east-1}
          endpoint_url = http://${BUCKET_HOST}:${BUCKET_PORT}
          EOF

          echo "=== AWS Config ==="
          cat ~/.aws/config

          echo "=== Bucket ==="
          echo "${BUCKET_NAME}"

          echo "=== Upload ==="
          echo "hello from rook-ceph" > /tmp/test.txt
          aws s3 cp /tmp/test.txt s3://${BUCKET_NAME}/test.txt

          echo "=== List ==="
          aws s3 ls s3://${BUCKET_NAME}/

          echo "=== Download ==="
          aws s3 cp s3://${BUCKET_NAME}/test.txt /tmp/downloaded.txt

          echo "=== Content ==="
          cat /tmp/downloaded.txt

          echo "=== Test completed ==="

          sleep 3600

      envFrom:
        - configMapRef:
            name: ceph-test-bucket

        - secretRef:
            name: ceph-test-bucket
EOF
```

