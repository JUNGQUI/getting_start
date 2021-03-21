### Dockerfile

도커를 통해 이미지를 만들고 (OS) 이를 통해 실제 서비스를 구축 할 수 있는데, 이 부분에 대해서는 사람의 손을 탈 수 밖에 없다.

예컨데 AWS EC2 인스턴스 위에서 mysql 과 기타 웹 서버를 구축했다 하더라도 이를 실제로 서버를 띄워서 사용하기까진 사람이 명령어를 직접 입력해야 한다.

그러나 이 일련의 과정을 script 로 만든다면 자동 빌드, 배포 까지도 가능하다. 이 script file 이 바로 Dockerfile 이다.

```dockerfile
# 실행하게 될 이미지
FROM ubuntu:14.04
# 일종의 표기
LABEL maintainer="ljk1112 <ljk2518@gmail.com>" "purpose"="practice"
# 도커 이미지가 실행되고 나서 실행하게 될 명령어
RUN apt-get update -y
# 상동
RUN apt-get install apache2 -y
# 현재 디렉토리에서 (로컬) 띄워진 도커 컨테이너 내부에 복사
ADD test.html /var/www/html
# 우분투 명령어로, cd 와 동일
WORKDIR /var/www/html
# 실행하게 될 명령어, /bin/bash shell 을 이용해서 hello -> test2.html 로 생성
# 현재 위치가 /var/www/html 이므로 여기에 생성
RUN ["/bin/bash", "-c", "echo hello >> test2.html"]
# 외부 포트를 80 으로 묶는다. 이렇게 한다고 다 되는건 아니고, 설정 또한 맞게 변경해야 한다.
# 기본적으로 80은 열려있으므로 사용 가능
EXPOSE 80
# 명령어를 실행하는데, 아파치서버를 포그라운드로 실행한다.
CMD apchectl -DFORGEGROUND
```

위와 같이 Dockerfile 을 만들고 `docker build -t BUILD_IMAGE_NAME ./` 를 실행하면 해당 Dockerfile 을 이용해서
새로운 이미지를 생성하고 명령어에 맞게 이미지를 생성 한다.

./를 이용한 것은 현재 디렉토리에서 `Dockerfile` 이란 이름을 가진 파일을 이용해서 도커 이미지를 생성하는것인데, 이 때 별도의 이름을 지정한다면
Dockerfile 이 아닌 다른 파일을 이용해서 이미지 생성이 가능하다. 값을 주지 않을 경우 기본적으로 Dockerfile 을 찾아서 실행한다.

위 명령어로 이미지 생성 후 `docker run -d -P --name myserver mybuild:0.0` 을 이용해서 컨테이너를 띄우면 아파치 서버가 잘 뜨고
test.html, test2.html 에 접근하면 모두 접근이 가능하다.

> 기본적으로 Dockerfile 을 통해 이미지를 생성 할 경우 해당 디렉토리에 있는 모든 파일을 같이 컴파일하는데, 이는 꽤 큰 자원 낭비이다.
> 
> gitignore 처럼 Docker 에도 ignore 가 있다.
> 
> ```ignorelang
> test2.html
> *.html
> */*.html (1depth directory 내의 모든 html 파일을 제외한다는 의미)
> test.htm?
> ```
> 
> 이와 같이 gitignore 와 유사하게 사용이 가능하다.

기본적으로 Dockerfile 을 통해 빌드를 할 경우 cache 기능이 있는데, 이를 통해 기존에 이미 받아온 이미지가 있을 경우 별도 빌드 없이 그대로 사용이 가능하다.

### 멀티 스테이지를 이용한 Docker build

```dockerfile
FROM golang
ADD main.go /root
WORKDIR /root
RUN go build -o /root/mainApp /root/main.go

FROM alpine:latest
WORKDIR /root
COPY --from=0 /root/mainApp .
CMD ["./mainApp"]
```

위에서 보면 FROM 이 두번 사용되었고, 각 FROM 을 기점으로 아래로 명령어가 작성되어 있는데, 이와 같이 하나의 Dockerfile 을 통해서 멀티 빌드가 가능하다.

위의 경우 첫번째는 golang 을 통해 go 이미지를 만들어내는데, 이후에 alpine 을 통해 root 에서 위에서 이미 띄웠던 go 이미지를 이용해서
alpine 내에서 복사하여 사용하고 있다.

이를 통한 이점은 하나의 Dockerfile 을 이용해서 사용 할 경우 위에서 사용하듯이 go 파일 자체의 기능은 별것 없지만 전체 go 관련된 이미지를 가져와서 사용하기에 해당 이미지의 리소스는 순수 go 리소스와 동일하다.

반면 위와 같이 구현할 경우 go 이미지를 실제로 띄우지 않고 생성만 한 상태에서 alpine 내로 복사를 해오기 때문에 go의 페이지는 그대로 사용하되 alpine 이미지가 빌드되기에 이미지 크기가 줄어들게 된다.

> alpine?
> 
> 아주 최소한의 기능만을 갖춘 리눅스 이미지로 이미지를 띄워서 테스트를 위한 용도의 배포판이다.

또한 `COPY --from=` 의 경우 0번째 즉, Dockerfile 내의 첫 FROM 부분에서 이미지가 사용되었다면 그 이미지에 결과를 가져와서 복사하겠다는 뜻이다.

alias 기능도 제공하기에 이와 같이도 사용이 가능하다.

```dockerfile
FROM golang as alias_go
ADD main.go /root
WORKDIR /root
RUN go build -o /root/mainApp /root/main.go

FROM alpine:latest
WORKDIR /root
COPY --from=alias_go /root/mainApp .
CMD ["./mainApp"]
```

