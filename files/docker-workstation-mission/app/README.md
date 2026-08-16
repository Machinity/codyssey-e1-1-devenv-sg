# app/ — 웹 서버 소스 & Dockerfile

- `Dockerfile` : nginx 베이스 커스텀 이미지 예시 (TODO 🔲 표시 부분을 채우거나, ubuntu/alpine
  베이스 방식(B)로 새로 작성해도 됩니다)
- `src/index.html` : 정적 콘텐츠. 바인드 마운트 실습 시 이 파일을 수정해 변경 반영을 확인하세요.

## 빠른 실행

```bash
cd app
docker build -t moosinsa-web:1.0 .
docker run -d -p 8080:80 --name moosinsa-web-8080 moosinsa-web:1.0
curl http://localhost:8080
```

결과/로그는 `../docs/06-custom-image-log.md`, `../docs/07-port-mapping-log.md`에 기록하세요.
