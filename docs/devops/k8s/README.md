!> hello k8s

```shell
kubectl --context=xxms-aliyun-live -n ai-app-live logs --tail -1 --max-log-requests=20 -l app=ai-app-service-teaching-api


```