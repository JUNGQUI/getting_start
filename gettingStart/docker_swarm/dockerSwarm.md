# Docker Swarm

- 클러스터링에 목적을 둔 도커 집합체로 보면 된다.

## docker swarm classic, docker swarm mode

두 케이스 모두 여러 도커 서버를 하나의 클러스터로 만들어 컨테이너를 생성하는 기능을 제공한다.

`docker swarm classic` 의 경우 컨테이너로서 클러스터링이 작동하기 때문에
`분산 코디네이터` 가 로드밸런서 역할을 하는 또 하나의 도커 서버와 연결이 되어 있으며 
이를 통해 다른 도커 서버와 통신을 하게 된다.

그에 반면 `docker swarm mode` 의 경우 도커 서버 내부에 `분산 코디네이터` 가 존재하여 일반적인 도커 서버처럼
관리되는 것이 아니고 로드밸런서등 다양한 부분에 대해 지원한다.

또한 매니저 - 에이전트 를 통해 매니저 노드 - 워커 노드 간 제어에 대해서도 편리하게 관리가 가능하다.

> 분산 코디네이터
> 
> 클러스터의 설정 저장, 데이터 동기화와 새로운 도커 서버 발견 및 로드밸런싱 역할을 수행한다. 이를 통해 분산 클러스터를 관리할 수 있는데
> swarm classic 의 경우 직접 도터 서버를 분산 코디네이터화 시켜야 하는 반면, swarm mode 의 경우 spring boot 처럼
> 자연스럽게 붙여서 클러스터링이 가능하다.

`docker info | grep Swarm` 을 통해 swarm 정보를 볼 수 있는데, 초기 설정이 없기에 `Swarm: inactive` 가 출력된다.

`docker swarm init --advertise-addr MANAGER_IP_ADDRESS` 입력 시 MANAGER_IP_ADDRESS 를 가진 container 가
매니저 노드가 됨과 동시에 docker swarm 이 초기화 된다.