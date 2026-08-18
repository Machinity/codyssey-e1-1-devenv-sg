# 08. 바인드 마운트 반영 증거

`~/ros2_ws_demo`(호스트) ↔ `/ros2_ws`(컨테이너)를 바인드 마운트한 상태에서,
`python3 -m http.server`로 디렉터리 목록을 노출해 반영 여부를 확인

## 실행 (07단계에서 이미 실행 중이면 재사용 가능)

```bash
✅ $ docker run -d --name ros2-dev-bindmount -p 8082:8080 \
      -v ~/ros2_ws_demo:/ros2_ws ros2-dev:1.0 \
      bash -c "python3 -m http.server 8080 --directory /ros2_ws"
c9d6cbfced3da4eae83f1b2195d34dfcc5f02800ba913b1fddb033025c9e278a
```

## 변경 전

```bash
✅ $ curl http://localhost:8082/src/
<!DOCTYPE HTML>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Directory listing for /src/</title>
</head>
<body>
<h1>Directory listing for /src/</h1>
<hr>
<ul>
</ul>
<hr>
</body>
</html>
```
스크린샷: ✅ [/screenshots/bind-mount/port8082_before.png](../screenshots/bind-mount/port8082_before.png)

## 호스트 파일 변경

```bash
✅ $ echo "hello ros2" > ~/ros2_ws_demo/src/test.txt
```

## 변경 후 (컨테이너 재시작 없이 즉시 반영 확인)

```bash
✅ $ curl http://localhost:8082/src/
<!DOCTYPE HTML>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Directory listing for /src/</title>
</head>
<body>
<h1>Directory listing for /src/</h1>
<hr>
<ul>
<li><a href="test.txt">test.txt</a></li>
</ul>
<hr>
</body>
</html>

✅ $ curl http://localhost:8082/src/test.txt
hello ros2
```
스크린샷: ✅ [/screenshots/bind-mount/port8082_after.png](../screenshots/bind-mount/port8082_after.png)

## 관찰 메모
바인드 마운트는 호스트 디렉터리를 컨테이너 내부 경로에 그대로 연결하는 방식이라,
컨테이너를 재시작하지 않아도 호스트에서 생성/수정한 파일이 즉시 컨테이너 내부에
반영됨을 확인함. 실제 ROS2 워크스페이스(src/) 개발 시에도 이 방식으로 호스트에서
편집한 코드가 컨테이너 안에서 바로 빌드/실행 가능함.
