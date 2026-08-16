# 05. 컨테이너 실행 실습 로그

## hello-world 실행

```bash
🔲 $ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
4f55086f7dd0: Pull complete 
Digest: sha256:5dd0d3e6e255913fc30f90b9f2b1d359cc2cbdb48090cc4b65f1676e203243cc
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

## ubuntu 컨테이너 진입 및 명령 수행

```bash
🔲 $ docker run -it --name ubuntu-test ubuntu bash
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
617772c7d19b: Pull complete 
a7fb98a8eddd: Pull complete 
Digest: sha256:678c6550cc43645e08669028bc177f50be4e7c5b8cca677067b1914d4afc7a03
Status: Downloaded newer image for ubuntu:latest
🔲 root@ec8c410fde0a:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
🔲 root@ec8c410fde0a:/# echo "hello from container"
hello from container
🔲 root@ec8c410fde0a:/# exit
exit
```

## exec로 재진입 (attach와의 차이 관찰용)

```bash
🔲 $ docker start ubuntu-test
ubuntu-test
username@c6r6s8 e1-1 %
🔲 $ docker exec -it ubuntu-test bash
root@ec8c410fde0a:/# 
🔲 root@ec8c410fde0a:/# echo "still here"
still here
🔲 root@ec8c410fde0a:/# exit
exit
```

## 종료(stop) vs 유지(exec/attach) 관찰 메모
🔲 `docker run -it ... bash`에서 `exit`하면 컨테이너의 메인 프로세스가 끝나 컨테이너도
STOPPED 상태가 됨. 반면 `docker exec -it`는 이미 실행 중인 컨테이너에 새 프로세스로
접속하는 것이라, 이 셸에서 `exit`해도 컨테이너 자체(메인 프로세스)는 계속 실행됨.
`docker attach`는 메인 프로세스 자체에 다시 연결하는 것이라 여기서 exit하면 컨테이너가 종료됨.
