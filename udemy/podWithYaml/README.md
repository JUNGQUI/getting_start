
```yaml
apiVersion: v1 # 생성할 kube 의 버전 POD, Service : v1 / ReplicaSet, Deployment : apps/v1
kind: Pod # Pod, Service, ReplicaSet, Deployment
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
    containers:
      - name: nginx-container
        image: nginx
```

- kubectl run nginx --image=nginx : nginx image 로 nginx pod 을 만든다.
- kubectl describe pod NAME : NAME pod 의 상세 정보 출력
- kubectl delete pod NAME : NAME pod delete