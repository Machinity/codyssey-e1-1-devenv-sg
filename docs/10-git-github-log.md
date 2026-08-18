# 10. Git 설정 및 GitHub / VSCode 연동 증거

## Git 사용자 정보 및 기본 브랜치 설정

```bash
✅ $ git config --global user.name "..."
✅ $ git config --global user.email "..."
✅ $ git config --global init.defaultBranch main

✅ $ git config --list
credential.helper=keychain
user.name=username
user.email=username@abc.com
init.defaultbranch=main
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
core.precomposeunicode=true
remote.origin.url=https://github.com/username/codyssey-e1-1-devenv-sg.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.main.remote=origin
branch.main.merge=refs/heads/main
branch.main.vscode-merge-base=origin/main
```

## VSCode ↔ GitHub 로그인 및 저장소 연동

1. ✅ VSCode에서 GitHub 계정 로그인 (Accounts 메뉴)
2. ✅ Source Control 탭에서 로컬 저장소 초기화(`git init`) 또는 클론
3. ✅ 원격 저장소(origin) 연결 및 첫 커밋/푸시 성공

```bash
✅ $ git remote -v
origin	https://github.com/Machinity/codyssey-e1-1-devenv-sg.git (fetch)
origin	https://github.com/Machinity/codyssey-e1-1-devenv-sg.git (push)

✅ $ git add .
✅ $ git commit -m "..."
✅ $ git push origin main
오브젝트 나열하는 중: 11, 완료.
오브젝트 개수 세는 중: 100% (11/11), 완료.
Delta compression using up to 6 threads
오브젝트 압축하는 중: 100% (6/6), 완료.
오브젝트 쓰는 중: 100% (6/6), 512 bytes | 512.00 KiB/s, 완료.
Total 6 (delta 4), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (4/4), completed with 4 local objects.
To https://github.com/Machinity/codyssey-e1-1-devenv-sg.git
   03f7bdd..0c84aad  main -> main
```

연동 증거 스크린샷: ✅ [screenshots/github-vscode/](../screenshots/github-vscode/)

## Git vs GitHub 역할 차이 메모
Git은 로컬에서 변경 이력을 관리하는 분산 버전관리 시스템이고, GitHub은 그 Git
저장소를 원격에서 호스팅하며 협업 기능(PR, 이슈, 코드리뷰, Actions 등)을 제공하는
클라우드 플랫폼이다.

## 보안 확인
- [x] 위 로그/스크린샷에 토큰, 비밀번호, 개인키가 노출되지 않음을 확인함
