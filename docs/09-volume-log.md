# 09. Docker 볼륨 영속성 검증

## 볼륨 생성 및 연결

```bash
$ docker volume create mydata
mydata
$ docker run -d --name vol-test -v mydata:/data ubuntu sleep infinity
824e8de18791b3d7e1d6b43033becf473bed90e86bc07718df58839565b834a0
$ docker exec -it vol-test bash -lc "echo hi > /data/hello.txt && cat /data/hello.txt"
hi
```

## 컨테이너 삭제

```bash
✅ $ docker rm -f vol-test
vol-test
```

## 새 컨테이너로 같은 볼륨 재연결 → 데이터 유지 확인

```bash
✅ $ docker run -d --name vol-test2 -v mydata:/data ubuntu sleep infinity
c0129541456e19353a31625f5b519ca4085e02ccaf82979b317f94cf353c5939
✅ $ docker exec -it vol-test2 bash -lc "cat /data/hello.txt"
hi
```

스크린샷/로그: ✅ <../screenshots/volume/volume_test.png>

## 관찰 메모
볼륨은 컨테이너 생명주기와 독립적으로 Docker가 관리하는 별도의 저장 영역이라,
컨테이너를 삭제해도 볼륨 자체를 삭제하지 않는 한 데이터가 보존됨을 확인함.
