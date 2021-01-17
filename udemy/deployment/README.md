### Deployment

pod 가 하나의 web application, replicaset 은 이 pod 를 균일하게 유지해주는 장치라 한다면
deployment 는 이 모든 동작에 대해 control 하는 것이자 hierarchy 구조라고 할 때 가장 최상에 위치해 있는거라고 볼 수 있다.

말 그대로 `배포` 와 관련된 모든 것을 관리하는 것이다.

### command

- kubectl create -f deployment-definition.yaml
- kubectl get deployment
- kubectl get all

### solution

- kubectl describe TYPE NAME | grep -i KEY : NAME 을 가진 TYPE 에서 KEY 값을 가져온다.
- kubectl create deployment httpd-frontend --image=httpd:2.4-alpine
- kubectl scale deployment --replicas=3 httpd-frontend