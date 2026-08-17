# 04. Docker 기본 운영 명령 로그

```bash
✅ $ docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ros2-dev      1.0       5dd5cc9d36b1   3 hours ago    5.27GB
<none>        <none>    87d68235fc1b   3 hours ago    5.27GB
ubuntu        latest    86a1a31fdd84   3 weeks ago    100MB
hello-world   latest    e2ac70e7319a   4 months ago   10.1kB

✅ $ docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED       STATUS       PORTS                                         NAMES
c0129541456e   ubuntu         "sleep infinity"          2 hours ago   Up 2 hours                                                 vol-test2
c9d6cbfced3d   ros2-dev:1.0   "/ros_entrypoint.sh …"   3 hours ago   Up 3 hours   0.0.0.0:8082->8080/tcp, [::]:8082->8080/tcp   ros2-dev-bindmount
02bddeb5052f   ros2-dev:1.0   "/ros_entrypoint.sh …"   3 hours ago   Up 3 hours   0.0.0.0:8081->8080/tcp, [::]:8081->8080/tcp   ros2-dev-8081
b36647bf7921   ros2-dev:1.0   "/ros_entrypoint.sh …"   3 hours ago   Up 3 hours   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp   ros2-dev-8080
ec8c410fde0a   ubuntu         "bash"                    4 hours ago   Up 4 hours                                                 ubuntu-test

✅ $ docker ps -a
CONTAINER ID   IMAGE          COMMAND                   CREATED       STATUS                   PORTS                                         NAMES
c0129541456e   ubuntu         "sleep infinity"          2 hours ago   Up 2 hours                                                             vol-test2
c9d6cbfced3d   ros2-dev:1.0   "/ros_entrypoint.sh …"   3 hours ago   Up 3 hours               0.0.0.0:8082->8080/tcp, [::]:8082->8080/tcp   ros2-dev-bindmount
02bddeb5052f   ros2-dev:1.0   "/ros_entrypoint.sh …"   3 hours ago   Up 3 hours               0.0.0.0:8081->8080/tcp, [::]:8081->8080/tcp   ros2-dev-8081
b36647bf7921   ros2-dev:1.0   "/ros_entrypoint.sh …"   3 hours ago   Up 3 hours               0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp   ros2-dev-8080
ec8c410fde0a   ubuntu         "bash"                    4 hours ago   Up 4 hours                                                             ubuntu-test
626bee27f9f9   hello-world    "/hello"                  4 hours ago   Exited (0) 4 hours ago                                                 infallible_cannon

✅ $ docker logs 626b
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

✅ $ docker run -it ubuntu bash
Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

✅ $ docker stats --no-stream
CONTAINER ID   NAME                 CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O     PIDS
c0129541456e   vol-test2            0.00%     1.633MiB / 15.67GiB   0.01%     830B / 126B       516kB / 0B    1
c9d6cbfced3d   ros2-dev-bindmount   0.02%     10.47MiB / 15.67GiB   0.07%     7.88kB / 6.32kB   63MB / 0B     1
02bddeb5052f   ros2-dev-8081        0.02%     10.55MiB / 15.67GiB   0.07%     6.78kB / 4.79kB   46MB / 0B     1
b36647bf7921   ros2-dev-8080        0.02%     10.61MiB / 15.67GiB   0.07%     6.03kB / 4.07kB   47.9MB / 0B   1
ec8c410fde0a   ubuntu-test          0.00%     668KiB / 15.67GiB     0.00%     3.69kB / 126B     15.6MB / 0B   1
```

## 관찰 메모
`docker ps`는 실행 중인 컨테이너만, `docker ps -a`는 종료된 컨테이너까지 모두 표시함
