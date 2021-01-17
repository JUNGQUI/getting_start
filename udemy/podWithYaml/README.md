
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
- kubectl run NAME --image=IMAGE --dry-run=client -o yaml > POD_NAME.yaml : NAME 을 이름으로 가지며 IMAGE 를
이미지로 삼되 해당 내역을 POD_NAME.yaml 로 만듬
- kubectl edit pod NAME : NAME pod 을 수정. 이름, 이미지 등을 수정할 수 있음
- kubectl get pod NAME -o yaml > pod-definition.yaml : 떠있는 pod NAME 의 설정을 yaml 로 만들고 
  pod-definition.yaml 파일로 로컬에 저장
- 