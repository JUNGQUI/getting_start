# getting_start
시작하세요! 도커와 쿠버네티스 책을 읽으면서 정리한 repository 입니다.

# docker command

일반적으로 image 의 format 은 다음과 같다.

리포지토리_이름/이미지_이름:버전

- docker run -i -t ubuntu:14.04 -> image 를 받으면서 시작하고 (run) bash shell 을 활성화하고 (-t) command line 으로 들어가는(-t) 명령어
  - docker run -it -p 80:80 --name mywebserver ubuntu:14.04 -> 외부포트와 내부포트(80-외부:80-내부) 를 연결하고 (-p) container 실행
  - docker run -it -p 80:80 -p 3000:80 --name mywebserver ubuntu:14.04 -> 이와 같이 multi port 지정도 가능
- docker pull centos:7 -> centos:7 image 를 받는 명령어
- docker image ls | docker images -> 이미지 목록
- docker container ls | docker ps -> 컨테이너 목록 (실행 상태의 컨테이너만)
  - -a : 정지상태도 포함
- docker create -it --name mycentos centos:7 -> 컨테이너를 생성하는데 (create) -it옵션을 주고 (위와 동일) 이름을 `mycentos`로 지정하고 (--name) 생성 시 centos:7 이미지를 이용한다.
- docker start mycentos -> `mycentos` 라는 컨테이너를 실행한다.
- docker attach mycentos -> `mycentos` 라는 컨테이너의 bashshell 에 접속한다. (-it option 덕에 사용)
- docker stop mycentos -> `mycentos` 라는 컨테이너를 정지
- docker container rm ID|NAME | docker rm NAME -> container 삭제
  - docker rm $(docker ps -a -q) -> 가동여부와 상관 없이 모두 삭제, -a 는 all, -p 는 container ID
  - docker container prune

# 도커 볼륨 by option

- docker run -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7 -> background 로 실행하고 (-d) 컨테이너 환경변수를 지정한다(-e)
- docker run -d -e WORDPRESS_DB_PASSWORD=password --name wordpress --link wordpressdb:mysql -p 80 wordpress -> 기존에 존재하는 container wordpressdb에 접속하는데, wordpress 컨테이너는 이를 `mysql` 로 인>식하며  나머지는 상동
  - -link : ip 로 접속이 가능하나 모든 컨테이너가 순차적으로 IP 를 부여받기 때문에 일일이 IP 로 연결하기 어려운 점이 있다. 이를 별칭으로 접근이 가능하다.
  - 포트 설정과 동일하게 외부명칭:내부명칭 으로 쓰인다.
- docker run -d --name wordpressdb_hostvolume -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -v /home/wordpress_db:/var/lib/mysql mysql:5.7
  - -v : db 설정 시 외부 볼륨을 사용하는데, 해당 볼륨의 Location, 외부 path:내부 path 에 위치시킨다.
    - v 옵션은 여러개 가능하며 file 단위도 가능하다.
  - --volumes-from CONTAINER_NAME : 이미 host 를 통해 사용중인 볼륨을 공유 받는 옵션, 이를 통해 
- docker run -d --name wordpress_hostvolume -e WORDPRESS_DB_PASSWORD=password --link wordpressdb_hostvolume:mysql -p 80 wordpress

# 도커 볼륨

옵션이 쉽지만 정상적으로 사용하려면 docker volume 을 이용하는게 여러모로 이용하기 편리하다.

- docker volume create --name myvolume
  - --name 과 같은 option 은 공통 옵션이라 어느 명령어에서나 사용이 가능하다.
- docker run -it --name myvolume_1 -v myvolume:/root/ ubuntu:14.04
  - myvolume 을 볼륨으로 지정한다. 위의 option 방식과는 다른 점은 path 대신 별칭을 쓴다는 것이다.
  - 또 하나의 다른 점은 --volume-from option 이 기본적으로 적용된다는 점이다.
- docker run -it --name myvolume_2 -v myvolume:/root/ ubuntu:14.04
  - 덧씌울 것 같지만 볼륨명을 지정해주었기에 덧씌우는게 아닌 공유하게 된다.

# 도커 네트워크

각 도커 컨테이너에는 veth 가 있다. 이는 virtual eth 라는 의미인데, 이 값들이 docker0 이라는 bridge 에 연결되고 이 bridge 를 통해
local 의 eth 와 연결된다.

- docker network create --driver bridge mybridge
- docker run -it --name mynetwork_container --net mybridge ubuntu:14.04
- docker network disconnect mybridge mynetwork_container
- docker network connect mybridge mynetwork_container
- docker network create --driver=bridge --subnet=172.72.0.0/16 --ip-range=172.72.0.0/24 --gateway=172.72.0.1 my_custom_network

# 컨테이너 로깅

컨테이너도 물론 로깅이 가능하다. 기본적으로 설정이 없을 경우 stdOut(json-file), stdErr(ERROR) 를 제공한다.

- docker logs CONTAINER_NAME -> 컨테이너 명의 로그를 출력한다.
- docker logs -tail 2 CONTAINER_NAME -> 마지막 줄로부터 2줄만 출력한다.
- docker logs --since TIME CONTAINER_NAME -> unix timestamp 로 작성하여 그 시간 이후부터의 로그만을 출력한다.
- docker logs -f -t CONTAINER_NAME -> 로그에 타임스탬프를 출력하고(-t) 스트림으로 확인이 가능하다(-f).

일반적인 log file 위치는 /var/lib/docker/containers/${CONTAINER_ID}/${CONTAINER_ID}-json.log 에 존재한다.

또한 OS 에 따라 시스템 로그또한 기록하고 저장하게 되는데, 기본적인 시스템 로그를 따라간다.

즉, centOS 의 경우 /var/log/... 에 파일로 저장되며, 우분투 16.04, CoreOS 의 경우는 journalctl -u docker.service 를 통해 확인이 가능하다.

## rsyslog

log 결과를 다른 곳으로 보낼 수 있는데 rsyslog 설정을 통해 가능하다.

- docker run -it -h rsyslog --name rsyslog_server -p 514:514 -p 514:514/udp ubuntu:14.04
- ~ : vi /etc/rsyslog.conf
  
```shell
# provides UDP syslog reception
$ModLoad imudp
$UDPServerRun 514

# provides TCP syslog reception
$ModLoad imtcp
$InputTCPServierRun 514
```

- ~ : service rsyslog restart 

이와 같은 설정을 통해 외부로 로그를 보내는 통신을 뚫어놓는다.

- docker run -it --log-driver syslog --log-opt syslog-address=tcp://192.168.0.100:514 --log-opt tag="maillog" --log-opt syslog-facility="email" ubuntu:14.04
  - --log-opt : 로그를 활용하기 위한 여러가지 옵션 지정
    - syslog-address : 로그 전송 IP
    - tag : 말 그대로 태그로, 로그 자체를 구분하기 위함 (한 row)
    - syslog-facility : 로그가 저장되는 파일의 명칭을 바꾸는 명령어, 로그 구분을 위함 (한 file)

