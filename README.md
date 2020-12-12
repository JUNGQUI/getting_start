# getting_start
시작하세요! 도커와 쿠버네티스 책을 읽으면서 정리한 repository 입니다.

# docker command

일반적으로 image 의 format 은 다음과 같다.

리포지토리_이름/이미지_이름:버전

- docker run -i -t ubuntu:14.04 / image 를 받으면서 시작하고 (run) bash shell 을 활성화하고 (-t) command line 으로 들어가는(-t) 명령어
  - docker run -it -p 80:80 --name mywebserver ubuntu:14.04 / 외부포트와 내부포트(80-외부:80-내부) 를 연결하고 (-p) container 실행
  - docker run -it -p 80:80 -p 3000:80 --name mywebserver ubuntu:14.04 / 이와 같이 multi port 지정도 가능
- docker pull centos:7 / centos:7 image 를 받는 명령어
- docker image ls | docker images / 이미지 목록
- docker container ls | docker ps / 컨테이너 목록 (실행 상태의 컨테이너만)
  - -a : 정지상태도 포함
- docker create -it --name mycentos centos:7 / 컨테이너를 생성하는데 (create) -it옵션을 주고 (위와 동일) 이름을 `mycentos`로 지정하고 (--name) 생성 시 centos:7 이미지를 이용한다.
- docker start mycentos / `mycentos` 라는 컨테이너를 실행한다.
- docker attach mycentos / `mycentos` 라는 컨테이너의 bashshell 에 접속한다. (-it option 덕에 사용)
- docker stop mycentos / `mycentos` 라는 컨테이너를 정지
- docker container rm ID|NAME | docker rm NAME / container 삭제
  - docker rm $(docker ps -a -q) / 가동여부와 상관 없이 모두 삭제, -a 는 all, -p 는 container ID
  - docker container prune

- docker run -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7 / background 로 실행하고 (-d) 컨테이너 환경변수를 지정한다(-e)
- docker run -d -e WORDPRESS_DB_PASSWORD=password --name wordpress --link wordpressdb:mysql -p 80 wordpress / 기존에 존재하는 container wordpressdb에 접속하는데, wordpress 컨테이너는 이를 `mysql` 로 인식하며  나머지는 상동
  - -link : ip 로 접속이 가능하나 모든 컨테이너가 순차적으로 IP 를 부여받기 때문에 일일이 IP 로 연결하기 어려운 점이 있다. 이를 별칭으로 접근이 가능하다.
  - 포트 설정과 동일하게 외부명칭:내부명칭 으로 쓰인다.

