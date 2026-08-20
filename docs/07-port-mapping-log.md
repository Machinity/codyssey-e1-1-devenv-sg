# 07. 포트 매핑 접속 증거

`ros2-dev:1.0` 이미지는 웹 서버가 아니므로, 컨테이너 안에서 `python3 -m http.server`를
임시로 실행해 포트 매핑을 검증 (자세한 실행 방법은 [`app/README.md`](../app/README.md) 참고)

## 1차 실행 (host 포트 8080)

```bash
✅ $ mkdir -p ~/ros2_ws_demo/src
✅ $ docker run -d --name ros2-dev-8080 -p 8080:8080 \
      -v ~/ros2_ws_demo:/ros2_ws ros2-dev:1.0 \
      bash -c "python3 -m http.server 8080 --directory /ros2_ws"
b36647bf7921d5b94acb0937383fc4e00c5b9062dfe9eb48c5c55c8fadee0fb7

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
<li><a href="src/">src/</a></li>
</ul>
<hr>
</body>
</html>
```

브라우저 접속 스크린샷: ✅ [/screenshots/port-mapping/port8080.png](../screenshots/port-mapping/port8080.png)

## 2차 실행 (다른 host 포트 8081)

```bash
✅ $ docker run -d --name ros2-dev-8081 -p 8081:8080 \
      -v ~/ros2_ws_demo:/ros2_ws ros2-dev:1.0 \
      bash -c "python3 -m http.server 8080 --directory /ros2_ws"
02bddeb5052f354035788bf497f95a18ed28f3e75ba6119a1de974a7ab5b3158

✅ $ curl http://localhost:8081
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
<li><a href="src/">src/</a></li>
</ul>
<hr>
</body>
</html>
```

브라우저 접속 스크린샷: ✅ [/screenshots/port-mapping/port8081.png](../screenshots/port-mapping/port8081.png)

## 포트 매핑이 필요한 이유 메모
컨테이너는 격리된 네트워크 네임스페이스를 가지므로, 호스트에서 컨테이너 내부 포트에
직접 접근할 수 없다. `-p host:container`로 호스트 포트와 컨테이너 포트를 연결해야
외부에서 서비스에 접근 가능하다. 같은 컨테이너 내부 포트(8080)를 서로 다른 host
포트(8080, 8081)로 동시에 노출할 수 있음을 두 번의 실행으로 확인함.

## 포트 충돌 진단
1. 일반적인 경우:
    1) 에러 메시지로 TCP/UDP, 포트 번호, 에러 종류(점유 vs 거부) 특정
    2) 호스트/컨테이너 구분해서 실제 점유 주체 확인 (ss -tulpn, docker ps/docker port)
    3) TIME_WAIT처럼 킬 불가능한 상태인지, 아니면 실제 살아있는 프로세스인지 구분
    4) 죽이기 전에 왜 떠 있는지 원인 파악 (좀비 vs 정상 서비스)
    5) 죽일 수 있으면 킬 후 재확인, 안 되면 포트 변경 — 단, DDS처럼 범위 기반인 경우 개별 포트가 아니라 설정 레벨에서 접근
    6) 변경 후 재현 테스트로 검증, 그리고 재발 가능성 있으면 원인(좀비 프로세스 발생 스크립트 등) 별도 수정

2. '호스트 포트가 이미 사용중'이라는 메세지가 확인된 경우:  
    Bind for 0.0.0.0:8080 failed: port is already allocated  
    또는   
    Error starting userland proxy: listen tcp4 0.0.0.0:8080: bind: address already in use  
    에 헤당하는 경우로,  
    1) 정말 호스트 프로세스가 문제인지, 아니면 다른 컨테이너인지 먼저 구분
    docker ps --format "table {{.Names}}\t{{.Ports}}" | grep 8080  
    여기서 잡히면 docker stop <name>으로 끝나는 문제
    2) 컨테이너에서 안 잡히면 host OS 레벨 확인  
    sudo lsof -i :8080  
    또는  
    sudo ss -tulpn | grep 8080
    여기서 PID가 나오면 그게 진짜 host 프로세스  
    3) PID는 없는데 메시지는 계속 뜨는 경우 (자주 발생)  
    ps aux | grep docker-proxy
    이걸로 확인하고, Docker 데몬 자체를 재시작하는 게 제일 확실(docker restart 아니라 Docker Desktop/OrbStack 자체 재시작)