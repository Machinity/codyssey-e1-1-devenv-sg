# 개발 워크스테이션 구축 — 터미널 / Docker / Git·GitHub

> ✅ 표시된 부분은 미션 요구사항입니다.
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
| OS | ProductName:		macOS
ProductVersion:		15.7.4 |
| Shell / Terminal | zsh |
| 컨테이너 런타임 | OrbStack — 서울캠퍼스 sudo 제약으로 Docker Desktop 대신 사용 |
| Docker 버전 | Docker version 28.5.2, build ecc6942 |
| Git 버전 | git version 2.53.0 |

```bash
✅ $ sw_vers
ProductName:		macOS
ProductVersion:		15.7.4
BuildVersion:		24G517

✅ $ echo $SHELL
/bin/zsh

✅ $ orb version
Version: 2.0.5 (2000500)
Commit: cfe47627f138ffd822c958553b0a93eaf2692c71 (v2.0.5)

✅ $ docker --version
Docker version 28.5.2, build ecc6942

✅ $ git --version
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
- [x] Git 설정 + VSCode-GitHub 연동 (`docs/10-git-github-log.md`, `screenshots/github-vscode/`)
- [x] 트러블슈팅 2건 이상 (`docs/11-troubleshooting.md`)
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
- **문제**: 커스텀 이미지로 도커 컨테이너 빌드시 UID/GID 관련 권한 설정 문제로 실패
- **원인 가설**: 권한 설정 부분 코드 수정이 필요할 것으로 추측
- **확인 방법**: 빌드 오류 로그 확인
- **해결/대안**: 해당 위치 코드 수정하여 해결

### 사례 2
- **문제**: 사례 1 코드 수정 후 빌드 재시도 했으나, 캐시 오류로 빌드 실패
- **원인 가설**: 도커 빌드 실패할 경우, 간헐적으로 실패 캐시가 남아 다음 빌드가 실패할 경우가 있다는 정보를 접함
- **확인 방법**: 코드 수정분은 정상 반영되어있는 걸 확인
- **해결/대안**: --no-cache 옵션을 명령어에 사용하여 해결

---

## 5. 과제 목표 개념 정리 (자기 설명)

✅ **절대 경로 vs 상대 경로**
절대 경로는 루트(Root) 디렉터리를 기점으로 전체 계층 구조를 모두 표기하는 방식으로, 내 로컬 환경에서는 실행 위치와 관계없이 항상 동일한 파일을 참조하므로 데이터 로드의 안정성이 높다. 반면, 상대 경로는 현재 작업 디렉터리(PWD)를 기준으로 .(현재)이나 ..(상위) 등의 축약어를 사용해 파일 위치를 지정한다. 프로그램을 타 장치에 배포하거나 이식할 때, 사용자마다 루트 이하의 디렉터리 구조나 사용자명이 다를 수 있다. 따라서 배포용 프로그램이나 프로젝트 내부 자원을 참조할 때는 애플리케이션 구조 내에서 유연하게 대응할 수 있는 상대 경로로 작성하는 것이 훨씬 안전하고 이식성이 높다.

✅ **파일 권한 (r/w/x, 755, 644)**
숫자 위치:
첫번째 - User / 두번째 - Group / 세번째 - Other

숫자별 권한(모든 권한은 r=4, w=2, x=1의 합으로 표시):
0 - 없음 / 1 - Execute / 2 - Write / 4 - Read

예시:
755 = 소유자 rwx / 그룹 r-x / 기타 r-x
644 = 소유자 rw- / 그룹 r-- / 기타 r--.

✅ **기존 Dockerfile 기반 커스텀 이미지**
<app/Dockerfile>

✅ **포트 매핑이 필요한 이유**
컨테이너는 격리된 네트워크 네임스페이스를 가지므로, 호스트에서 컨테이너 내부 포트에
직접 접근할 수 없다. `-p host:container`로 호스트 포트와 컨테이너 포트를 연결해야
외부에서 서비스에 접근 가능하다. 같은 컨테이너 내부 포트(8080)를 서로 다른 host
포트(8080, 8081)로 동시에 노출할 수 있음을 두 번의 실행으로 확인함.

✅ **Docker 볼륨(영속 데이터)**
볼륨은 컨테이너 생명주기와 독립적으로 Docker가 관리하는 별도의 저장 영역이라,
컨테이너를 삭제해도 볼륨 자체를 삭제하지 않는 한 데이터가 보존됨을 확인함.

✅ **Git vs GitHub 역할 차이**
Git은 로컬에서 변경 이력을 관리하는 버전관리 시스템이고, GitHub은 그 Git 저장소를 원격에서 호스팅하며 협업(PR, 이슈, 코드리뷰 등)을 지원하는 플랫폼이다.

---

## 6. 보너스 과제 (선택, 수행 시 체크)

- [x] Docker Compose 기초 — [bonus/docker-compose.yml](./bonus/docker-compose.yml)
- [x] Compose 멀티 컨테이너
- [x] Compose 운영 명령어 (up/down/ps/logs)
- [x] 환경 변수 활용
- [x] GitHub SSH 키 설정

✅ 보너스 수행 내용 요약:
운영 관점에서 docker를 어떻게 관리하는 것이 더 나을지, github 활용시 ssh와 https 인증 방식의 차이가 어떤 것인지 확인함

---

## 7. 재현 방법 (평가자용)

```bash
# 1) 저장소 클론
git clone https://github.com/Machinity/codyssey-e1-1-devenv-sg.git
cd codyssey-e1-1-devenv-sg

# 2) 커스텀 이미지 빌드 및 실행
cd app
docker build -t ros2-dev:1.0 .
mkdir -p ~/ros2_ws_demo/src
docker run -d --name ros2-dev-8080 -p 8080:8080 \
  -v ~/ros2_ws_demo:/ros2_ws ros2-dev:1.0 \
  bash -c "python3 -m http.server 8080 --directory /ros2_ws"

# 3) 접속 확인
curl http://localhost:8080
```

---

## 8. 보안 체크

- [x] 토큰/비밀번호/개인키/인증 코드가 로그·스크린샷에 노출되지 않았는지 확인함
- [x] 노출된 민감정보가 있었다면 즉시 제거 및 재발급 처리함
