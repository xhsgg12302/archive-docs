!> hello k8s

```shell
kubectl --context=xxms-aliyun-live -n ai-app-live logs --tail -1 --max-log-requests=20 -l app=ai-app-service-teaching-api

kubectl --kubeconfig=meta.config -n test-back get secret harbor-key -o jsonpath='{.data}'
echo 'eyJtZXRhLXJlZ2lzdHJ5LmNuLWhhbmd6aG91LmNyLmFsaXl1bmNzLmNvbSI6eyJ1c2VybmFtZSI6Indhbmdob25nYmFvQDE3MDMwNTI5NzA2MTQ5NDUiLCJwYXNzd29yZCI6ImN4Y0RTZTNDWDlDWEMwMTMyYkNDWCJ9fQa==' | base64 -d
```