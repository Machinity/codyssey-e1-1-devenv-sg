# 12. 보너스 과제 로그

`bonus/docker-compose.yml`은 `app/Dockerfile`(ROS2 Jazzy 커스텀 이미지)을 빌드해 실행하는
`ros2-dev` 서비스와, 보조 서비스인 `cache`(redis)로 구성

---

## 1) Docker Compose 기초 — 단일 서비스 실행

```bash
✅ $ cd bonus
✅ $ docker compose up -d ros2-dev
[+] Building 2.4s (13/13) FINISHED                                                                                     
 => [internal] load local bake definitions                                                                        0.0s
 => => reading from stdin 663B                                                                                    0.0s
 => [internal] load build definition from Dockerfile                                                              0.1s
 => => transferring dockerfile: 4.61kB                                                                            0.0s
 => [internal] load metadata for docker.io/osrf/ros:jazzy-desktop-full                                            1.6s
 => [internal] load .dockerignore                                                                                 0.1s
 => => transferring context: 2B                                                                                   0.0s
 => [1/7] FROM docker.io/osrf/ros:jazzy-desktop-full@sha256:9c23fdebf4e723118b3a0cabf95fa213fc8667258bc02a34f526  0.0s
 => CACHED [2/7] RUN apt-get update && apt-get install -y     git     wget     curl     vim     build-essential   0.0s
 => CACHED [3/7] RUN rosdep update                                                                                0.0s
 => CACHED [4/7] RUN if getent group 1000 > /dev/null 2>&1; then         EXISTING_GROUP=$(getent group 1000 | cu  0.0s
 => CACHED [5/7] WORKDIR /ros2_ws                                                                                 0.0s
 => CACHED [6/7] RUN chown -R ros:ros /ros2_ws                                                                    0.0s
 => CACHED [7/7] RUN echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc     && echo "source /ros2_ws/install/s  0.0s
 => exporting to image                                                                                            0.1s
 => => exporting layers                                                                                           0.0s
 => => writing image sha256:3e737dcad5bd79a66ac3f8c80f3b10f64b513e84b8ebc143c5071764152a933a                      0.0s
 => => naming to docker.io/library/bonus-ros2-dev                                                                 0.0s
 => resolving provenance for metadata file                                                                        0.0s
[+] Running 3/3
 ✔ bonus-ros2-dev              Built                                                                              0.0s 
 ✔ Network bonus_default       Created                                                                            0.1s 
 ✔ Container ros2-dev-compose  Started                                                                            0.5s 

✅ $ curl http://localhost:8080
<!DOCTYPE HTML>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Directory listing for /</title>
</head>
<body>
<h1>Directory listing for /</h1>
<hr>
<ul>
</ul>
<hr>
</body>
</html>
```

## 배움 포인트 메모
`docker run -d -p 8080:8080 -v ./workspace:/ros2_ws ros2-dev:1.0 bash -c "..."`처럼
매번 길게 치던 실행 옵션이 `docker-compose.yml`이라는 "문서화된 실행 설정"으로 정리되어,
`docker compose up -d` 한 줄이면 항상 같은 방식으로 재현 가능해짐을 확인함.

---

## 2) Docker Compose 멀티 컨테이너 — web + 보조 서비스

```bash
✅ $ docker compose up -d
[+] Running 8/8
 ✔ cache Pulled                                                                                                   5.9s 
   ✔ e6f31ffc071e Pull complete                                                                                   0.8s 
   ✔ aae5c34d2458 Pull complete                                                                                   0.9s 
   ✔ dcaf2c69e5bf Pull complete                                                                                   1.1s 
   ✔ 5c39d5b7aae4 Pull complete                                                                                   2.3s 
   ✔ 59b5200b7471 Pull complete                                                                                   2.4s 
   ✔ 4f4fb700ef54 Pull complete                                                                                   2.5s 
   ✔ 5107053fcce4 Pull complete                                                                                   2.6s 
[+] Running 2/2
 ✔ Container cache-compose     Started                                                                            0.5s 
 ✔ Container ros2-dev-compose  Running                                                                            0.0s 

✅ $ docker compose ps
NAME               IMAGE            COMMAND                   SERVICE    CREATED         STATUS         PORTS
cache-compose      redis:alpine     "docker-entrypoint.s…"   cache      2 minutes ago   Up 2 minutes   6379/tcp
ros2-dev-compose   bonus-ros2-dev   "/ros_entrypoint.sh …"   ros2-dev   2 minutes ago   Up 2 minutes   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp
```

### 컨테이너 간 네트워크 통신 확인 (서비스 디스커버리)

`ros2-dev` 이미지에는 `iproute2`/`iputils-ping`이 이미 설치되어 있어 별도 설치 없이 확인 가능함

```bash
✅ $ docker compose exec ros2-dev ping -c 3 cache
PING cache (192.168.97.3) 56(84) bytes of data.
64 bytes from cache-compose.bonus_default (192.168.97.3): icmp_seq=1 ttl=64 time=0.044 ms
64 bytes from cache-compose.bonus_default (192.168.97.3): icmp_seq=2 ttl=64 time=0.051 ms
64 bytes from cache-compose.bonus_default (192.168.97.3): icmp_seq=3 ttl=64 time=0.051 ms

--- cache ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2022ms
rtt min/avg/max/mdev = 0.044/0.048/0.051/0.003 ms
```

## 배움 포인트 메모
✅ Compose가 만든 기본 네트워크 안에서는 서비스 이름(`cache`)이 곧 컨테이너의
호스트명으로 자동 등록되어, IP 주소를 몰라도 이름만으로 통신 가능함을 `ping`으로 확인함.

---

## 3) Compose 운영 명령어 습득 (up / down / ps / logs)

```bash
✅ $ docker compose ps
NAME               IMAGE            COMMAND                   SERVICE    CREATED         STATUS         PORTS
cache-compose      redis:alpine     "docker-entrypoint.s…"   cache      4 minutes ago   Up 4 minutes   6379/tcp
ros2-dev-compose   bonus-ros2-dev   "/ros_entrypoint.sh …"   ros2-dev   4 minutes ago   Up 4 minutes   0.0.0.0:8080->8080/tcp, [::]:8080->8080/tcp

✅ $ docker compose logs ros2-dev
ros2-dev-compose  | 192.168.97.1 - - [17/Aug/2026 08:36:11] "GET / HTTP/1.1" 200 -

✅ $ docker compose down
[+] Running 3/3
 ✔ Container cache-compose     Removed                                                                            0.4s 
 ✔ Container ros2-dev-compose  Removed                                                                           10.3s 
 ✔ Network bonus_default       Removed                                                                            0.1s 

✅ $ docker compose ps
NAME      IMAGE     COMMAND   SERVICE   CREATED   STATUS    PORTS
```

## 배움 포인트 메모
✅ `up`으로 띄우고 `ps`/`logs`로 상태를 확인한 뒤 `down`으로 정리하는 흐름이,
개별 `docker run`/`docker stop`/`docker rm`을 일일이 하는 것보다 훨씬 간단한
"운영 관점 상태 확인 루틴"이 됨을 체감함.

---

## 4) 환경 변수 활용 — 포트/모드 변경

```bash
✅ $ cat .env
HOST_PORT=8080
HTTP_PORT=8080

# .env의 HOST_PORT를 8090으로 바꾼 뒤 재실행
✅ $ docker compose up -d ros2-dev
✅ $ curl http://localhost:8090
<!DOCTYPE HTML>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Directory listing for /</title>
</head>
<body>
<h1>Directory listing for /</h1>
<hr>
<ul>
</ul>
<hr>
</body>
</html>
```

## 배움 포인트 메모
✅ 포트 번호가 `docker-compose.yml`이나 Dockerfile에 하드코딩되어 있지 않고 `.env`로
분리되어 있어, 설정값 변경 시 이미지를 다시 빌드하거나 코드를 고칠 필요가 없음을 확인함.
이것이 "설정과 코드의 분리"가 실무에서 갖는 의미라고 이해함.

---

## 5) GitHub SSH 키 설정

```bash
✅ $ ssh-keygen -t ed25519 -C "your_email@example.com"
Enter file in which to save the key (/Users/username/.ssh/id_ed25519): ..
Enter passphrase for "/Users/username/.ssh/id_ed25519: ..

✅ $ eval "$(ssh-agent -s)"
✅ $ ssh-add --apple-use-keychain ~/.ssh/id_ed25519
Identity added: ..
✅ $ cat ~/.ssh/id_ed25519.pub
ssh-ed25519 ..

✅ $ ssh -T git@github.com
Hi Machinity! You've successfully authenticated, but GitHub does not provide shell access.
```

원격 저장소를 HTTPS → SSH로 전환:
```bash
✅ $ git remote set-url origin git@github.com:machinity/https://github.com/Machinity/codyssey-e1-1-devenv-sg.git.git
✅ $ git remote -v
origin	git@github.com:machinity/https://github.com/Machinity/codyssey-e1-1-devenv-sg.git.git (fetch)
origin	git@github.com:machinity/https://github.com/Machinity/codyssey-e1-1-devenv-sg.git.git (push)

✅ $ git push
오브젝트 나열하는 중: 41, 완료.
오브젝트 개수 세는 중: 100% (41/41), 완료.
Delta compression using up to 6 threads
오브젝트 압축하는 중: 100% (38/38), 완료.
오브젝트 쓰는 중: 100% (39/39), 1.61 MiB | 9.33 MiB/s, 완료.
Total 39 (delta 1), reused 18 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (1/1), done.
To github.com:Machinity/codyssey-e1-1-devenv-sg.git
   18a49c1..b5ebb65  main -> main
```

## 배움 포인트 메모
HTTPS 방식은 매번 토큰 입력이 필요하거나 credential helper에 저장해야 하는 반면,
SSH 키는 개인키를 로컬에 안전하게 보관하고 공개키만 GitHub에 등록해두면 인증을 반복할
필요가 없다는 점에서 인증 방식과 보안 습관의 차이를 이해함.

## 보안 확인
- [x] 개인키(`id_ed25519`, 확장자 없는 파일)를 로그/스크린샷/커밋에 포함하지 않았음을 확인함
- [x] 공개키(`.pub`)만 GitHub에 등록했음을 확인함
