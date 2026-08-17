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

브라우저 접속 스크린샷: ✅ (../screenshots/port-mapping/port8080.png)

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

브라우저 접속 스크린샷: ✅ (../screenshots/port-mapping/port8081.png)

## 포트 매핑이 필요한 이유 메모
컨테이너는 격리된 네트워크 네임스페이스를 가지므로, 호스트에서 컨테이너 내부 포트에
직접 접근할 수 없다. `-p host:container`로 호스트 포트와 컨테이너 포트를 연결해야
외부에서 서비스에 접근 가능하다. 같은 컨테이너 내부 포트(8080)를 서로 다른 host
포트(8080, 8081)로 동시에 노출할 수 있음을 두 번의 실행으로 확인함.
