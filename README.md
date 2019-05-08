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
```

