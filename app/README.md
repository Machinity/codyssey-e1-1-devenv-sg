# app/ — ROS2 Jazzy 개발용 커스텀 이미지

- `Dockerfile` : `osrf/ros:jazzy-desktop-full`(Ubuntu 24.04 + ROS2 Jazzy) 베이스에
  개발 도구, Nav2/SLAM 관련 ROS2 패키지, 비root 사용자, 환경변수(`ROS_DOMAIN_ID` 등)를
  추가한 커스텀 이미지. 요구사항 (B) "Linux 베이스 + 패키지/사용자/환경변수 추가" 방식.

이 이미지는 웹 서버가 아니라 ROS2 개발 환경이므로, **포트 매핑 / 바인드 마운트 증거는
컨테이너 안에서 `python3 -m http.server`를 임시로 띄워** 만듭니다. (추가 패키지 설치 없이
Python이 이미지에 기본 포함되어 있어 바로 사용 가능)

## 1) 빌드

```bash
cd app
docker build -t ros2-dev:1.0 .
```

## 2) 실행 (포트 매핑 + 바인드 마운트를 한 번에 검증)

호스트에 워크스페이스 폴더를 만들고, 컨테이너의 `/ros2_ws`에 바인드 마운트합니다.

```bash
mkdir -p ~/ros2_ws_demo/src
cd ~/ros2_ws_demo

docker run -d \
  --name ros2-dev-8080 \
  -p 8080:8080 \
  -v ~/ros2_ws_demo:/ros2_ws \
  ros2-dev:1.0 \
  bash -c "python3 -m http.server 8080 --directory /ros2_ws"
```

## 3) 포트 매핑 접속 확인

```bash
curl http://localhost:8080
```
브라우저로 `http://localhost:8080` 접속 시 `/ros2_ws` 디렉터리 목록(`src/` 등)이 보이면
포트 매핑이 정상 동작하는 것입니다. 서로 다른 host 포트(예: 8081)로 한 번 더 실행해
2회 이상 증거를 남기세요.

## 4) 바인드 마운트 반영 확인

```bash
echo "hello ros2" > ~/ros2_ws_demo/src/test.txt
# 컨테이너 재시작 없이 새로고침/curl만으로 목록에 test.txt가 즉시 나타나는지 확인
curl http://localhost:8080/src/
```

## 5) ROS2 개발 환경으로도 사용

```bash
docker run -it --rm -v ~/ros2_ws_demo:/ros2_ws ros2-dev:1.0
ros2 --version
```

결과/로그는 `../docs/06-custom-image-log.md`, `../docs/07-port-mapping-log.md`,
`../docs/08-bind-mount-log.md`에 기록하세요.
