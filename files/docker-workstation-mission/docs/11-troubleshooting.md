# 11. 트러블슈팅 로그 (2건 이상)

각 사례는 **문제 → 원인 가설 → 확인 → 해결/대안** 순서로 작성합니다.

---

## 사례 1

**문제**
🔲 (예: `docker: permission denied while trying to connect to the Docker daemon socket`)

**원인 가설**
🔲 (예: 서울캠퍼스 sudo 권한 제약으로 일반 Docker 데몬에 접근 권한이 없을 것으로 추정)

**확인**
```bash
🔲 $ docker info
🔲 (에러 출력)
```

**해결/대안**
🔲 (예: OrbStack 설치 및 실행 → 내부적으로 sudo 없이 접근 가능한 Docker 엔진이 구동되어
동일한 `docker` 명령을 그대로 사용할 수 있게 됨)

---

## 사례 2

**문제**
🔲

**원인 가설**
🔲

**확인**
```bash
🔲 $ 
🔲 (출력)
```

**해결/대안**
🔲

---

## (선택) 사례 3
🔲 필요 시 동일한 형식으로 추가
