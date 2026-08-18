# 06. 커스텀 이미지 제작 로그

## 선택한 방식
Linux 베이스 이미지(ubuntu 계열) + 패키지/사용자/환경변수 추가

## 사용한 기존 베이스
- 베이스 이미지: `osrf/ros:jazzy-desktop-full` (OSRF 공식 이미지, Ubuntu 24.04 Noble + ROS2 Jazzy + RViz2 + Gazebo 포함)
- 참고: https://hub.docker.com/r/osrf/ros

## 적용한 커스텀 포인트

| 커스텀 포인트 | 목적 |
|---|---|
| `ARG USERNAME/USER_UID/USER_GID` | 팀원마다 다를 수 있는 사용자 정보를 빌드 인자로 분리해 재사용성 확보 |
| 개발 도구 설치 (git, curl, vim, build-essential, cmake 등) | 컨테이너 내부에서 바로 빌드/디버깅 가능한 개발 환경 구성 |
| ROS2 패키지 설치 (navigation2, nav2-bringup, slam-toolbox, ros2-control 등) | Nav2/SLAM 기반 로봇 개발에 필요한 핵심 패키지를 이미지에 미리 포함 |
| `rosdep update` | 이후 패키지 의존성을 자동으로 해결할 수 있도록 초기화 |
| 비root 사용자 생성 (`groupadd`/`useradd` + sudo) | 컨테이너를 root로 실행하지 않아 보안 강화, 바인드 마운트 시 파일 권한 문제 방지 |
| `WORKDIR /ros2_ws` + `chown` | 작업 디렉터리를 비root 사용자 소유로 지정해 권한 오류 없이 개발 가능 |
| `.bashrc`에 `source /opt/ros/jazzy/setup.bash` 자동 추가 | 컨테이너 진입 시마다 수동으로 ROS2 환경을 source하지 않아도 되도록 자동화 |
| `ENV ROS_DOMAIN_ID=42`, `ROS_LOCALHOST_ONLY=0` | 여러 팀원이 같은 네트워크에서 ROS2 통신할 때 도메인 충돌을 피하고, 로컬호스트로 제한하지 않도록 설정 |
| 캐시 삭제 (`rm -rf /var/lib/apt/lists/*`) | 불필요한 apt 캐시를 제거해 이미지 용량 최소화 |

Dockerfile 원본은 [`app/Dockerfile`](../app/Dockerfile) 참고.

## 빌드 및 실행 로그

```bash
✅ $ cd app
✅ $ docker build -t ros2-dev:1.0 .
[+] Building 94.7s (11/11) FINISHED                                                                            docker:orbstack
 => [internal] load build definition from Dockerfile                                                                      0.1s
 => => transferring dockerfile: 4.16kB                                                                                    0.0s
 => [internal] load metadata for docker.io/osrf/ros:jazzy-desktop-full                                                    1.0s
 => [internal] load .dockerignore                                                                                         0.1s
 => => transferring context: 2B                                                                                           0.0s
 => CACHED [1/7] FROM docker.io/osrf/ros:jazzy-desktop-full@sha256:9c23fdebf4e723118b3a0cabf95fa213fc8667258bc02a34f5263  0.0s
 => [2/7] RUN apt-get update && apt-get install -y     git     wget     curl     vim     build-essential     cmake       70.1s
 => [3/7] RUN rosdep update                                                                                               9.1s 
 => [4/7] RUN if getent group 1000 > /dev/null 2>&1; then         EXISTING_GROUP=$(getent group 1000 | cut -d: -f1) &&    9.5s 
 => [5/7] WORKDIR /ros2_ws                                                                                                0.2s 
 => [6/7] RUN chown -R ros:ros /ros2_ws                                                                                   0.4s 
 => [7/7] RUN echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc     && echo "source /ros2_ws/install/setup.bash 2>/de  0.4s 
 => exporting to image                                                                                                    3.4s 
 => => exporting layers                                                                                                   3.3s 
 => => writing image sha256:87d68235fc1b51afb96f56f3fcaa18cc0748a96a971b55faa7cbee5a0fbf18a0                              0.0s 
 => => naming to docker.io/library/ros2-dev:1.0      

✅ $ docker run -it --rm ros2-dev:1.0 bash -c "echo \$ROS_DISTRO; echo \$ROS_DOMAIN_ID; whoami"
jazzy
42
ros
```

빌드/실행 스크린샷: ✅ [screenshots/custom-image/custom_build.png](../screenshots/custom-image/custom_build.png)

## 검증 메모
`whoami` 실행 시 root가 아닌 `ros` 사용자로 진입되는지, `echo $ROS_DOMAIN_ID`로
환경변수가 42로 설정되어 있는지 등을 확인하여 커스텀 포인트가 실제로 적용되었음을 검증함
