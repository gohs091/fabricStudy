# Dockerfile

## Build
```shell

docker build -t [생성할 이미지명] : [태그명] [Dockerfile의 위치]
ex) docker build -t sample:1.0 /home/docker/sample
# 이미지 명 : sample 
# 태그 1.0
# DockerFile 위치 /home/docker/sample/
 
```

## RUN 
```shell

RUN [실행하고 싶은 명령]
1) Shell 형식으로 기술
/bin/sh에서 실행됨
ex) RUN apt-get install -y nginx

2) Exec 형식으로 기술
Shell을 경유하지 않고 직접 실행
$HOME과 같은 환경변수를 지정할 수 없고, 명령을 JSON 배열로 지정
ex) RUN ["/bin/bash","-c","apt-get install -y nginx"]
```

## CMD
```shell

CMD [실행하고 싶은 명령]
ex) CMD ["nginx", "-g", "daemon off;"] - Exec
    CMD nginx -g 'daemon off;' -shell



이미지를 바탕으로 생성된 컨테이너에서 실행할 명령
Dockerfile에는 하나의 CMD 명령을 기술, 여러 개를 지정하면 마지막 명령만 유효
```

## ENTRYPOINT
```shell

ENTRYPOINT [실행하고 싶은 명령]
ex) ENTRYPOINT ["nginx", "-g", "daemon off;"]
    ENTRYPOINT nginx -g 'daemon off';
ENTRYPOINT 명령에서 지정한 명령은 docker container run 명령을 실행했을 때 실행된다.

CMD 명령과의 차이는 docker container run 명령 실행 시의 동작에 있다.
CMD 명령은 docker container run 명령 실행 시에 인수로 새로운 명령을 지정하면 지정한 명령을 우선 실행 시킨다.

따라서 

FROM Ubuntu:16.04
ENTRYPOINT ["top"]
CMD["-d","10"]

라는 내용의 Dockerfile을 만들고

docker container run -it sample
docker container run -it sample -d 2 를 하면
ENTRYPOINT 는 그대로 실행되지만 CMD 의 내용은 -d 2 로 변경되어 덮어 씌어진다.


```

## ONBUILD
```shell

ONBUILD [실행하고 싶은 명령]
Dockerfile로부터 생성한 이미지를 베이스 이미지로 한 다른 Dockerfile을 빌드할 때 실행하고 싶은 명령을 기술합니다.

ex) FROM ubuntu:17.10
    ONBUILD ADD site.tar /var/www/html/

```

## ENV
```shell

ENV [key] [value]
ENV [key]=[value]

환경변수 설정
```

## WORKDIR
```shell

WORKDIR [작업 디렉토리 경로]
ex) WORKDIR /first
    WORKDIR second
    WORKDIR third
    RUN ["pwd"]

RUN,CMD,ENTRYPOINT,COPY,ADD 명령을 실행하기 위한 작업용 디렉토리를 지정
디렉토리가 존재하지 않으면 새로 작성합니다.
상대 경로를 지정한 경우는 이전 WORKDIR 명령의 경로에 대한 상대 경로가 됩니다.

```

## EXPOSE
```shell

EXPOSE <포트 번호>
ex) EXPOSE 8080

EXPOSE 명령은 Docker에게 실행 중인 컨테이너가 listen 하고 있는 네트워크를 알려줍니다.
또한 docker container run 명령의 -p 옵션을 사용할 때 어떤 포트를 호스트에 공개할지를 정의합니다.

```

## ARG 
```shell
ARG <이름>[=기본값]
ex) ARG YOURNAME="asa"
build시에 --build-arg 옵션을 붙이면 값을 변경할 수 있습니다.
```

## ADD
```shell
ADD <호스트의 파일 경로> <Docker 이미지의 파일 경로>
ex) ADD host.html /docker_dir/
```

## COPY
```shell
COPY <호스트의 파일 경로> <Docker 이미지의 파일 경로>
ADD 명령과 매우 비슷하지만 다른 점은 ADD 명령은 원격 파일의 다운로드나 아카이브의 압축 해제 등과 같은 기능을 갖고 있지만
COPY 명령은 단순히 복사 처리만 합니다.
```

## VOLUME
```shell
VOLUME ["/마운트 포인트"]
-v <컨테이너 디렉토리> ex) sudo docker run -i -t --name hello-volume -v /data ubuntu /bin/bash
-v <호스트 디렉토리>:<컨테이너 디렉터리> ex) sudo docker run -it --name hello-volume1 -v /root/data:/data ubuntu /bin/bash

데이터 볼륨 설정을 통해 컨테이너 끼리 혹은 호스트와 컨테이너가 데이터를 공유할 수 있다.


```


# Docker-compose 

## docker-compose.yml의 개요

YAML : YAML은 구조화된 데이터를 표현하기 위한 데이터 포맷입니다. YAML은 Python과 같이 들여쓰기로 데이터의 계층 구조를 나타냅니다. 들여쓰기는 탭이 아니라 반각 스페이스를 사용합니다.
YAML에서는 데이터의 맨 앞에 '-'를 붙이면 배열을 나타낼 수 있습니다. '-' 다음에는 반드시 반각 스페이스를 넣기 바랍니다.

## 이미지 지정 image 

```shell
services:
webserver:
   image: ubuntu
   
만약 Dockerfile을 작성해놓고 빌드하여 베이스 이미지로 지정할 때는 

services:
   webserver:
      build: . # . 은 커런트 디렉토리를 나타낸다.
 
```

## 컨테이너 안에서 명령 지정 command
```shell
command: /bin/bash
```

## 컨테이너 간 연결(links)
```shell
links:
   - logserver
```

## 컨테이너 간 통신(ports/expose)
```shell
컨테이너가 공개하는 포트는 ports로 지정합니다. '호스트 머신의 포트 번호:컨테이너의 포트번호'을 지정하거나
컨테이너의 포트 번호만 지정합니다. 또한 컨테이너의 포트 번호만 지정한 경우는 호스트 머신의 포트는 랜덤한 값으로 설정됩니다.

ports:
  - "3000"
  - "8000:8000"
  - "49100:22"
  - "127.0.0.1:8001:8001"
```

## 서비스의 의존관계 정의(depends_on)
```shell
services:
  webserver:
    build:
    depends_on:
      - db
      - redis
  redis:
     image: redis
  db:
     image: postgree
```

## 컨테이너 환경변수 지정(environment/env_file)
```shell

# 배열 형식으로 지정
environment:
  - HOGE=fuga
  - FOO
# 해시 형식으로 지정
environment:
  HOGE: fuga
  FOO:
  
env_file: envfile
```

## 컨테이너 데이터 관리(volumes/volumes_from)

```shell
컨테이너에 볼륨을 마운트할 때는 volumes를 지정합니다.
호스트 측에서 마운트할 경로를 지정하려면 호스트의 디렉토리 경로:컨테이너의 디렉토리 경로를 지정합니다.

volumes:
  - /var/lib/mysql
  - cache/:/tmp/cache

```


## Docker Compose의 주요 서브 명령
up 컨테이너 생성/시작
ps 컨테이너 목록 표시
logs 컨테이너 로그 출력
run 컨테이너 실행
start 컨테이너 시작
stop 컨테이너 정지
restart 컨테이너 재시작
pause 컨테이너 일시 정지
unpause 컨테이너 재개
port 공개 포트 번호 표시
config 구성 확인
kill 실행 중인 컨테이너 강제 정지
rm 컨테이너 삭제
down 리소스 삭제

# 여러 컨테이너 생성(up)

```shell
docker-compose up [옵션] [서비스명 .]


#옵션
-d 백그라운드에서 실행한다.
--build 이미지를 빌드한다. 
--no-build 이미지를 빌드하지 않는다.

2개의 컨테이너를 시작하는 예

version: '3.3'

services:
  server_a:
    image: nginx
    
  server_b:
    image: redis
    

```

# 여러 컨테이너 확인(ps/logs)

```shell
컨테이너들의 목록을 표시할 때는 docker-compose ps 명령을 사용합니다.

docker-compose ps
# -q 옵션을 지정하면 컨테이너 ID만 표시됩니다.

컨테이너의 로그를 확인하려면 docker-compose logs 명령을 사용합니다

```


