### replication controller

- yaml 정의
- kubectl create -f rc-definition.yaml 

### replicaset

- yaml 정의
- kubectl create -f replicaset-definition.yaml

### scale-up/down

- replicaset-definition.yaml 재정의 (replicas)
- kubectl replace -f replicaset-definition.yaml
- kubectl edit replicaset REPLICASET_NAME -> 직접 수정

- kubectl scale --replicas=6 -f replicaset-definition.yaml : replicaset-definition.yaml 의 replicas 를 수정
- kubectl scale --replicas=6 replicaset myapp-replicaset : myapp-replicaset(replicaset NAME) 이름을 가진 replicaset(TYPE)
의 replicas 를 6으로 수정
  
  `위 두가지 방법의 단점으론, file 을 수정하는 것이 아니라 yaml base reset 을 한다면 여전히 yaml 은 3이기에 3개의 replicas 를 유지하려 할 것이다.`
  
### delete

- kubectl delete replicaset myapp-replicaset


### solution

- kubectl get pod
- kubectl get replicaset
- kubectl describe pods
- kubectl describe replicaset
- kubectl apply -f YAML_NAME.yaml
- kubectl delete replicaset NAME : replicaset NAME 을 삭제


### Replication Controller VS ReplicaSet

Replication Controller 는 deprecated 된 구 버전의 replicaset 으로 볼 수 있다.

가장 큰 차이점은 replication controller 에는 selector 를 이용한 구분이 가능하다는 점이다.

```yaml
...
selector:
  matchExpressions:
    - key: app
      values: 
        - my-nginx-pods-label
        - your-nginx-pods-label
      operator: In
  template:
...
```
이 yaml 파일을 보면 `app:my-nginx-pods-label` 뿐만 아니라 `app:your-nginx-pods-label` 의 경우도
해당 Replica Set 으로써 관리하겠다는 의미이며 이는 `replicas` 개수에 해당 내역이 포함됨을 의미한다.

이와 같이 replication controller 는 deprecated 된 경우이니 그러려니 하고 replica set 을 사용하면 된다.