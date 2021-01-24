### namespace

pod 혹은 replicaset 내에서는 어떻게 하던 자원이 지정되어 있기에 (ex: replicaset 내에서 pod 이름 jklee 는 유일)
명령어 수행 혹은 작업 수행 시 이 부분이 오해가 생길 일이 없다.

하지만 서로 다른 replicaset A, B 가 있을 때 A - jklee pod 와 B - jklee pod 가 있을 떄 명령어 수행 시 docker 와 k8s
는 어떤 pod 에 수행해야 할지 모른다.

이에 따라 namespace 를 별도로 주어 지정해줄 수 있다.

여러 replica 가 올라가 CI/CD 를 목표로 하는 k8s 인 만큼 이 namespace 가 중요하게 작용한다.

- kubectl get pod (일반적)
- kubectl get pod --namespace=SOME_NAMESPACE (namespace 가 SOME_NAMESPACE 인 pod 만)

yaml 을 통해 생성시 namespace 지정

일반
- kubectl create -f pod-definition.yaml
  
지정
- kubectl create -f pod-definition.yaml --namespace=dev

혹은

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  namespace: dev # 이와 같이 metadata 밑에 namespace 를 지정할 수 있다.
  labels:
    app: myapp
spec:
  containers:
    - name: nginx-container
      image: nginx
```


```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

또는 이와 같이 yaml 을 통해 namespace 생성도 가능하다.

- kubectl create -f name-space.yaml
  
namespace + metadata 내 선언된 name 을 따라서 명령어 입력 시
- kubectl create namespace dev

와 같이 가능하다.


default namespace 변경 (모든 명령어의 기본 값을 해당 namespace 로 지정)

- kubectl config set-context $(kubectl config current-context) --namespace=dev
    - 이렇게 할 경우 기본 namespace 가 default -> dev 로 이동하게 된다.

이후 kubectl get pods 입력 시 dev namespace 가 기본적으로 지정되어 실행된다.

모든 namespace 검색 시 
- kubectl get pods --all-namespace
    - --all-namespace 옵션을 통해 지정 가능
    
> 꿀팁
> namespace 의 shortcut 은 ns 로 퉁칠 수 있다.
> - kubectl get namespace == kubectl get ns
> 
> --dry-run=client 
> - 실제 생성은 run 을 통해 자원까지 분배되지만 dry-run 옵션을 통해 생성은 하지 않고 맞는지, 자원 할당이 가능한지
> 등을 체크할 수 있다.
> 
> -o yaml > NAME.yaml
> - -o 옵션을 통해 설정을 파일로 만들 수 있고 파일 형식을 yaml 로 지정한다. 이후 > 명령어를 통해 이름을 지정 할 수 있는 것은
> 물론 path 지정도 가능하다.

    
### Resource Quota

management system 내에서 컴퓨팅 자원에 대한 명시

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```

이와 같이 yaml 생성 후
- kubectl create -f compute-quota.yaml


### 풀이

- kubectl get ns --no-headers | wc -l
    - ns : namespace shortcut
    - --no-headers : header 제거
    - | wc -l : 개수를 가져와서(wc) 뿌려준다(-l)
    
- kubectl get pods -n research
    - -n NAME : --namespace=NAME 과 동일
    
- kubectl run redis --image=redis --dry-run=client -o yaml > redis-example-pod.yaml
이후 해당 파일 수정 후 apply 혹은
- kubectl run redis --image=redis --namespace=finance

- db-service.dev.svc.cluster.local : DNS.NAMESPACE.SERVCE.CLUSTER.LOCAL 을 의미