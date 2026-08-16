# 개발 워크스테이션 구축 — 터미널 / Docker / Git·GitHub

> 🔲 표시된 부분은 실습 후 직접 작성/교체해야 하는 항목입니다.
> 이 문서 하나만 읽어도 전체 수행 내용과 증거 위치를 파악할 수 있도록 작성합니다.

---

## 0. 프로젝트 개요

**미션 목표 요약**
> 이 과제는 리눅스 CLI, Docker, Git/GitHub을 직접 세팅하고 사용하여 "내 컴퓨터에서만 되는" 문제를
> 줄이는 재현 가능한 개발 환경을 구성하는 것을 목표로 한다. 터미널 기본 조작과 권한 실습,
> Docker 설치/점검/운영, Dockerfile 기반 커스텀 이미지 제작, 포트 매핑, 바인드 마운트/볼륨 영속성 검증,
> Git/GitHub 연동까지 전 과정을 수행하고 로그로 남긴다.

---

## 1. 실행 환경

| 항목 | 값 |
|---|---|
| OS | 🔲 ProductName:		macOS
ProductVersion:		15.7.4 |
| Shell / Terminal | 🔲 zsh |
| 컨테이너 런타임 | 🔲 OrbStack — 서울캠퍼스 sudo 제약으로 Docker Desktop 대신 사용 |
| Docker 버전 | Docker version 28.5.2, build ecc6942 |
| Git 버전 | git version 2.53.0 |

```bash
$ docker --version
Docker version 28.5.2, build ecc6942
$ git --version
git version 2.53.0
```

---

## 2. 수행 항목 체크리스트

- [x] 터미널 기본 조작 및 폴더 구성 (`docs/01-terminal-log.md`)
- [x] 파일/디렉터리 권한 변경 실습 (`docs/02-permission-log.md`)
- [x] Docker 설치 및 기본 점검 (`docs/03-docker-install-log.md`)
- [x] Docker 기본 운영 명령 (images/ps/logs/stats) (`docs/04-docker-ops-log.md`)
- [x] hello-world / ubuntu 컨테이너 실행 실습 (`docs/05-container-run-log.md`)
- [x] Dockerfile 기반 커스텀 이미지 제작 (`docs/06-custom-image-log.md`, `app/Dockerfile`)
- [x] 포트 매핑 접속 증거 2회 이상 (`docs/07-port-mapping-log.md`, `screenshots/port-mapping/`)
- [x] 바인드 마운트 반영 증거 (`docs/08-bind-mount-log.md`, `screenshots/bind-mount/`)
- [x] Docker 볼륨 영속성 증거 (`docs/09-volume-log.md`, `screenshots/volume/`)
- [ ] Git 설정 + VSCode-GitHub 연동 (`docs/10-git-github-log.md`, `screenshots/github-vscode/`)
- [ ] 트러블슈팅 2건 이상 (`docs/11-troubleshooting.md`)
- [ ] (보너스) Docker Compose (`bonus/docker-compose.yml`)

---

## 3. 검증 방법 및 결과 위치 (한눈에 보기)

| 검증 항목 | 사용한 명령 | 무엇을 확인했나 | 결과 위치 |
|---|---|---|---|
| 터미널 기본 조작 | `pwd`, `ls -la`, `cd`, `mkdir`, `cp`, `mv`, `rm`, `touch`, `cat` | 절대/상대경로 이동, 파일 생성·복사·삭제 | [docs/01-terminal-log.md](./docs/01-terminal-log.md) |
| 권한 실습 | `ls -l`, `chmod 755/644`, `chmod +x` | 변경 전/후 권한 비교 | [docs/02-permission-log.md](./docs/02-permission-log.md) |
| Docker 설치 점검 | `docker --version`, `docker info` | 설치 여부, 데몬 동작 여부 | [docs/03-docker-install-log.md](./docs/03-docker-install-log.md) |
| Docker 운영 | `docker images`, `docker ps -a`, `docker logs`, `docker stats` | 이미지/컨테이너 목록, 로그, 리소스 | [docs/04-docker-ops-log.md](./docs/04-docker-ops-log.md) |
| 컨테이너 실행 | `docker run hello-world`, `docker run -it ubuntu` | 컨테이너 정상 실행, 내부 명령 수행 | [docs/05-container-run-log.md](./docs/05-container-run-log.md) |
| 커스텀 이미지 | `docker build`, `docker run` | 커스텀 이미지 빌드/실행 성공 | [docs/06-custom-image-log.md](./docs/06-custom-image-log.md), [app/Dockerfile](./app/Dockerfile) |
| 포트 매핑 | `docker run -p host:container`, 브라우저 접속 | 서로 다른 포트로 2회 이상 접속 성공 | [docs/07-port-mapping-log.md](./docs/07-port-mapping-log.md), [screenshots/port-mapping/](./screenshots/port-mapping/) |
| 바인드 마운트 | `docker run -v $(pwd):/path` | 호스트 변경이 컨테이너에 즉시 반영 | [docs/08-bind-mount-log.md](./docs/08-bind-mount-log.md), [screenshots/bind-mount/](./screenshots/bind-mount/) |
| 볼륨 영속성 | `docker volume create`, `docker rm -f`, 재생성 후 확인 | 컨테이너 삭제 후에도 데이터 유지 | [docs/09-volume-log.md](./docs/09-volume-log.md), [screenshots/volume/](./screenshots/volume/) |
| Git/GitHub | `git config --list`, VSCode Source Control | 사용자 정보, 원격 저장소 연동 | [docs/10-git-github-log.md](./docs/10-git-github-log.md), [screenshots/github-vscode/](./screenshots/github-vscode/) |

---

## 4. 트러블슈팅 (요약, 상세는 [docs/11-troubleshooting.md](./docs/11-troubleshooting.md))

### 사례 1
- **문제**: 🔲
- **원인 가설**: 🔲
- **확인 방법**: 🔲
- **해결/대안**: 🔲

### 사례 2
- **문제**: 🔲
- **원인 가설**: 🔲
- **확인 방법**: 🔲
- **해결/대안**: 🔲

---

## 5. 과제 목표 개념 정리 (자기 설명)

🔲 **절대 경로 vs 상대 경로**
> 예: 절대 경로는 `/` 루트부터 시작하는 고정 경로(`/home/user/app`)이고, 상대 경로는 현재 위치 기준
> 경로(`../app`, `./src`)이다. ...

🔲 **파일 권한 (r/w/x, 755, 644)**
> 예: r=읽기(4), w=쓰기(2), x=실행(1). 755는 소유자 rwx(7), 그룹 r-x(5), 기타 r-x(5)를 의미하며
> 실행 파일/디렉터리에 주로 사용. 644는 소유자만 쓰기 가능하고 나머지는 읽기만 가능하여 일반 파일에 사용. ...

🔲 **기존 Dockerfile 기반 커스텀 이미지**
> 예: ...

🔲 **포트 매핑이 필요한 이유**
> 예: ...

🔲 **Docker 볼륨(영속 데이터)**
> 예: ...

🔲 **Git vs GitHub 역할 차이**
> 예: Git은 로컬에서 변경 이력을 관리하는 버전관리 시스템이고, GitHub은 그 Git 저장소를 원격에서
> 호스팅하며 협업(PR, 이슈, 코드리뷰 등)을 지원하는 플랫폼이다. ...

---

## 6. 보너스 과제 (선택, 수행 시 체크)

- [ ] Docker Compose 기초 — [bonus/docker-compose.yml](./bonus/docker-compose.yml)
- [ ] Compose 멀티 컨테이너
- [ ] Compose 운영 명령어 (up/down/ps/logs)
- [ ] 환경 변수 활용
- [ ] GitHub SSH 키 설정

🔲 보너스 수행 내용 요약:

---

## 7. 재현 방법 (평가자용)

```bash
# 1) 저장소 클론
git clone 🔲<저장소_URL>
cd docker-workstation-mission

# 2) 커스텀 이미지 빌드 및 실행
cd app
docker build -t 🔲<image-name>:1.0 .
docker run -d -p 🔲<host_port>:🔲<container_port> --name 🔲<container-name> 🔲<image-name>:1.0

# 3) 접속 확인
curl http://localhost:🔲<host_port>
```

> 특정 개인 PC에 종속된 경로/설정이 있다면 여기에 대체 방법을 기록하세요. 🔲

---

## 8. 보안 체크

- [ ] 토큰/비밀번호/개인키/인증 코드가 로그·스크린샷에 노출되지 않았는지 확인함
- [ ] 노출된 민감정보가 있었다면 즉시 제거 및 재발급 처리함 (해당 시 기록) 🔲
