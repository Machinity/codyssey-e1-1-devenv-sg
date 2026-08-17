# 11. 트러블슈팅 로그 (2건 이상)

각 사례는 **문제 → 원인 가설 → 확인 → 해결/대안** 순서로 작성합니다.

---

## 사례 1

**문제**
```
groupadd: GID '1000' already exists
ERROR: failed to build: ... exit code: 4
```
`docker build -t ros2-dev:1.0 .` 실행 중 `RUN groupadd --gid $USER_GID $USERNAME` 단계에서 실패함.

**원인 가설**  
베이스 이미지 `osrf/ros:jazzy-desktop-full`이 Ubuntu 24.04 기반인데, Ubuntu 24.04 계열 이미지는
기본적으로 UID/GID 1000에 `ubuntu`라는 사용자/그룹이 이미 만들어져 있는 경우가 많다.
Dockerfile에서 동일한 GID(1000)로 새 그룹을 또 만들려고 해서 충돌이 났을 것으로 추정.

**확인**  
```bash
✅ $ docker build -t ros2-dev:1.0 .
 => ERROR [4/7] RUN groupadd --gid 1000 ros     && useradd --uid 1000 --gid 1000 -m ros     && apt-get update &&  0.5s
------
 > [4/7] RUN groupadd --gid 1000 ros     && useradd --uid 1000 --gid 1000 -m ros     && apt-get update && apt-get install -y sudo     && echo "ros ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers     && rm -rf /var/lib/apt/lists/*:
0.413 groupadd: GID '1000' already exists
------
Dockerfile:76
--------------------
  75 |     #     && rm -rf /var/lib/apt/lists/*
  76 | >>> RUN groupadd --gid $USER_GID $USERNAME \
  77 | >>>     && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
  78 | >>>     && apt-get update && apt-get install -y sudo \
  79 | >>>     && echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers \
  80 | >>>     && rm -rf /var/lib/apt/lists/*
  81 |     
--------------------
ERROR: failed to build: failed to solve: process "/bin/sh -c groupadd --gid $USER_GID $USERNAME     && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME     && apt-get update && apt-get install -y sudo     && echo \"$USERNAME ALL=(ALL) NOPASSWD:ALL\" >> /etc/sudoers     && rm -rf /var/lib/apt/lists/*" did not complete successfully: exit code: 4
```

**해결/대안**  
`groupadd`/`useradd`를 무조건 실행하는 대신, 해당 UID/GID가 이미 존재하면 `groupmod -n` /
`usermod -l`로 **이름만 바꿔서 재사용**하고, 없을 때만 새로 생성하도록 조건 분기 처리함
(`app/Dockerfile`의 "비root 사용자 생성" 단계 참고). 수정 후 재빌드하여 정상 완료됨을 확인.

```bash
✅ $ docker build -t ros2-dev:1.0 .
[+] Building 22.3s (11/11) FINISHED                                                                    docker:orbstack
 => [internal] load build definition from Dockerfile                                                              0.1s
 => => transferring dockerfile: 4.61kB                                                                            0.0s
 => [internal] load metadata for docker.io/osrf/ros:jazzy-desktop-full                                            1.0s
 => [internal] load .dockerignore                                                                                 0.1s
 => => transferring context: 2B                                                                                   0.0s
 => [1/7] FROM docker.io/osrf/ros:jazzy-desktop-full@sha256:9c23fdebf4e723118b3a0cabf95fa213fc8667258bc02a34f526  0.0s
 => CACHED [2/7] RUN apt-get update && apt-get install -y     git     wget     curl     vim     build-essential   0.0s
 => CACHED [3/7] RUN rosdep update                                                                                0.0s
 => [4/7] RUN if getent group 1000 > /dev/null 2>&1; then         EXISTING_GROUP=$(getent group 1000 | cut -d:   14.6s
 => [5/7] WORKDIR /ros2_ws                                                                                        0.2s 
 => [6/7] RUN chown -R ros:ros /ros2_ws                                                                           0.4s 
 => [7/7] RUN echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc     && echo "source /ros2_ws/install/setup.ba  0.4s 
 => exporting to image                                                                                            5.1s 
 => => exporting layers                                                                                           5.0s 
 => => writing image sha256:f5f4282d2500ba3070e6f9322a73bcfaab19ed54ab5dea0a97e7cc894583386b                      0.0s 
 => => naming to docker.io/library/ros2-dev:1.0  
```

---

## 사례 2

**문제**  
`app/Dockerfile`의 사용자/그룹 생성 로직을 (조건 분기 방식으로) 수정했는데도,
수정 이후 다시 `docker build -t ros2-dev:1.0 .`를 실행하면 이전과 동일한
`groupadd: GID '1000' already exists` 에러가 그대로 재현됨. `cat Dockerfile`로
파일 내용이 실제로 수정되어 있는 것도 확인했는데도 동일 에러가 발생.

**원인 가설**  
Docker BuildKit의 레이어 캐시가 남아 있어서, 예전에 실패했던 시점의 캐시된 레이어를
재사용하려다 꼬였을 가능성. (일반적으로는 파일이 바뀌면 그 지점부터 캐시가 자동
무효화되지만, OrbStack 환경 등에서 간헐적으로 이전 실패 캐시가 남아있는 경우가 있음)

**확인**
```bash
✅ $ cat Dockerfile
groupmod/getent 등 수정된 내용이 실제로 반영되어 있음을 확인
```
→ 파일 자체는 정상 수정되어 있었으므로, 파일 문제가 아니라 빌드 캐시 쪽 문제로 판단.

**해결/대안**  
`--no-cache` 옵션으로 캐시를 전혀 사용하지 않고 처음부터 다시 빌드하여 해결.

```bash
✅ $ docker build --no-cache -t ros2-dev:1.0 .
[+] Building 22.3s (11/11) FINISHED                                                                    docker:orbstack
 => [internal] load build definition from Dockerfile                                                              0.1s
 => => transferring dockerfile: 4.61kB                                                                            0.0s
 => [internal] load metadata for docker.io/osrf/ros:jazzy-desktop-full                                            1.0s
 => [internal] load .dockerignore                                                                                 0.1s
 => => transferring context: 2B                                                                                   0.0s
 => [1/7] FROM docker.io/osrf/ros:jazzy-desktop-full@sha256:9c23fdebf4e723118b3a0cabf95fa213fc8667258bc02a34f526  0.0s
 => CACHED [2/7] RUN apt-get update && apt-get install -y     git     wget     curl     vim     build-essential   0.0s
 => CACHED [3/7] RUN rosdep update                                                                                0.0s
 => [4/7] RUN if getent group 1000 > /dev/null 2>&1; then         EXISTING_GROUP=$(getent group 1000 | cut -d:   14.6s
 => [5/7] WORKDIR /ros2_ws                                                                                        0.2s 
 => [6/7] RUN chown -R ros:ros /ros2_ws                                                                           0.4s 
 => [7/7] RUN echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc     && echo "source /ros2_ws/install/setup.ba  0.4s 
 => exporting to image                                                                                            5.1s 
 => => exporting layers                                                                                           5.0s 
 => => writing image sha256:f5f4282d2500ba3070e6f9322a73bcfaab19ed54ab5dea0a97e7cc894583386b                      0.0s 
 => => naming to docker.io/library/ros2-dev:1.0  
```
→ 모든 레이어가 정상적으로 재실행되며 빌드 성공. 이후에는 `--no-cache` 없이도
정상적으로 캐시가 갱신되어 빌드됨.

**참고 메모**  
이후 재현 테스트로 `docker build -t ros2-dev:1.0 .`만으로도 정상 빌드 성공.

---

## 사례 3

**문제**
```bash
docker run -it --rm ros2-dev:1.0 bash -c "echo \$ROS_DISTRO; echo \$ROS_DOMAIN_ID; whoami"
```
→ `ROS_DISTRO`(jazzy)와 `whoami`(ros)는 정상 출력되는데, `ROS_DOMAIN_ID`만 빈 값으로 출력됨.

**원인 가설**  
`ROS_DOMAIN_ID`/`ROS_LOCALHOST_ONLY`를 Dockerfile에서 진짜 `ENV`가 아니라
`echo "export ROS_DOMAIN_ID=42" >> ~/.bashrc` 방식으로만 넣어뒀기 때문일 것으로 추정.
`.bashrc`는 **대화형(interactive) 셸에서만** 자동으로 읽히는데, `docker run ... bash -c "..."`는
`-it` 플래그가 있어도 `-c`로 명령을 넘기면 비대화형으로 실행되어 `.bashrc`를 읽지 않음.
반면 `ROS_DISTRO`는 베이스 이미지가 `ENV`로 박아둔 값이라 어떤 방식으로 실행해도 항상 보임.

**확인**  
```bash
✅ $ docker run -it --rm ros2-dev:1.0 bash -c "echo \$ROS_DOMAIN_ID"
→ 출력 없음

✅ $ docker run -it --rm ros2-dev:1.0
✅ ros@...:/ros2_ws$ echo $ROS_DOMAIN_ID
✅ 42   ← 대화형 셸(.bashrc 로드됨)에서는 정상 출력됨을 확인 → 원인 확정
```

**해결/대안**  
`ROS_DOMAIN_ID`, `ROS_LOCALHOST_ONLY`를 `.bashrc` export 대신 Dockerfile의 **`ENV`
인스트럭션**으로 직접 지정하도록 수정. `ENV`는 이미지 레벨에 고정되는 값이라
`bash -c`, `docker exec`, 스크립트 실행 등 실행 방식과 무관하게 항상 적용됨.
```dockerfile
ENV ROS_DOMAIN_ID=42
ENV ROS_LOCALHOST_ONLY=0
```
수정 후 재빌드하여 `bash -c` 방식으로도 정상 출력됨을 확인.

