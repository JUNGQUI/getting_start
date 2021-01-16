# pod

## 파드(pod)

container 들이 여럿 군집을 이뤄 하나의 `객체` 처럼 사용하는 단위를 pod 이라고 한다.

또한 주 container 역할에 주 container 를 보조하는 보조 container 들을 사이드카 컨테이너 라고 한다.

예를 들자면 nginx 를 주 container 하나로 만들되, 이를 보조하기 위한 설정 reloader, 로그 수집을 하는 프로세스등을 사이드카 container 라고 볼 수 있다.

보통의 pod 들은 위와 같은 구성을 주로 사용한다.

## 레플리카셋

일정 개수의 pod 를 유지하는 컨트롤러이다.

이를 사용하는 이유는 장애 대응 및 여러 pod 의 로드밸런싱을 위함이다.

개발자가 일일이 pod 를 정의해서 이와 같이 실행 할 수도 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx-pod-a
spec:
  containers:
    - name: my-nginx-container
      image: nginx:latest
      ports:
      - containerPort: 80
        protocol: TCP
---
apiVersion: v1
kind: Pod
metadata:
  name: my-nginx-pod-b
spec:
  containers:
    - name: my-nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
          protocol: TCP
```

다만 이런식으로 YAML file 을 생성해서 pod 을 유지할 경우 다시 노드가 생성되지 않으며 pod 만 남아있다.

하지만 replica set 을 이용할 경우 지속적으로 유지되며 설정 또한 간편하다.

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-nginx
# replica setting start
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-nginx-pods-label
# label 이 my-nginx-pods-label 인 template 을 선택한다.
# replica setting end

# container setting start
template:
  metadata:
    name: my-nginx-pod
    labels:
      app: my-nginx-pods-label
  spec:
    containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
# container setting end
```

이와 같은 방식으로 YAML file 을 만들어서 적용하면, 어떠한 pod 에 error 가 발생해도 `replicas 3` 으로 지정했기에
항상 3개의 pod 로 유지하게 된다.

이렇게 한 후에 `kubectl apply -f replicaset-nginx.yaml` (위 file 을 replicaset-nginx.yaml 이라 하자) 수행하면 
항상 3가지의 pod 이 떠있게 된다.

원리로는 matchLabels 가 지정한 label 을 가지는 replica set 이 설정값에 남긴 개수와 같을 때까지 늘리게 된다. 그렇기에 label 이 중요하고, template 내의 label 도, metadata 의 label 도 중요하다.

