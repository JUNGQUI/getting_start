### CMD

앞서 Dockerfile 에 다양한 CMD (RUN, FROM 등) 가 있었는데 그 중 유용한 몇 가지를 작성하고자 한다.

#### ENV

Dockerfile 내에서의 환경변수를 설정한다.

```dockerfile
...
ENV test /home
...
```

이와 같이 Dockerfile 을 생성 할 때 ENV 를 지정하면 생성된 이미지를 통해 컨테이너가 만들어졌을 때 컨테이너 내부에서 환경변수에 접근하면

```
root@oaifjhaifs:/home echo $test
/home
```

이와 같이 사용이 가능하다.

물론 build 후 run 시 환경변수를 덧씌운다면 당연하게도 덧씌워진 값을 사용하게 된다.

#### VOLUME

VOLUME 이 DB 처럼 사용하게 될 부분이 아니라 컨테이너 내부에서 호스트 서버와 공유할 directory 정보를 기입 할 수 있다.

```dockerfile
FROM unbuntu:14.04
RUN mkdir /home/volume
RUN echo test >> /home/volume/testfile
VOLUME /home/volume
```

기타 명령어도 많지만 굳이 정리할 필요 없이 필요에 따라 그때 그때 찾아보면서 사용하면 될 듯 하다.