# 01. 터미널 기본 조작 로그

요구된 항목: 현재 위치 확인, 목록 확인(숨김 파일 포함), 이동, 생성, 복사, 이동/이름변경, 삭제,
파일 내용 확인, 빈 파일 생성

**명령어 + 출력 결과**

```bash
✅ $ pwd
/Users/username/e1-1/codyssey-e1-1-devenv-sg

✅ $ ls -la
total 24
drwxr-xr-x   6 username  username   192  8 16 15:18 .
drwxr-xr-x   3 username  username    96  8 16 15:05 ..
-rw-r--r--@  1 username  username  6148  8 16 15:11 .DS_Store
drwxr-xr-x  13 username  username   416  8 16 15:17 .git
drwx------@  6 username  username   192  8 16 15:18 files
-rw-r--r--   1 username  username    78  8 16 15:05 README.md

✅ $ mkdir -p ~/codyssey/practice
✅ $ cd ~/codyssey/practice

✅ $ touch empty.txt
✅ $ ls -la
total 0
drwxr-xr-x  3 username  username  96  8 16 15:23 .
drwxr-xr-x  3 username  username  96  8 16 15:23 ..
-rw-r--r--  1 username  username   0  8 16 15:23 empty.txt

✅ $ cp empty.txt copy.txt
✅ $ ls -la
total 0
drwxr-xr-x  4 username  username  128  8 16 15:33 .
drwxr-xr-x  3 username  username   96  8 16 15:23 ..
-rw-r--r--  1 username  username    0  8 16 15:33 copy.txt
-rw-r--r--  1 username  username    0  8 16 15:23 empty.txt

✅ $ mv copy.txt renamed.txt
✅ $ ls -la
total 0
drwxr-xr-x  4 username  username  128  8 16 15:33 .
drwxr-xr-x  3 username  username   96  8 16 15:23 ..
-rw-r--r--  1 username  username    0  8 16 15:23 empty.txt
-rw-r--r--  1 username  username    0  8 16 15:33 renamed.txt

✅ $ echo "hello" > renamed.txt
✅ $ cat renamed.txt
hello

✅ $ rm renamed.txt
✅ $ ls -la
total 0
drwxr-xr-x  3 username  username  96  8 16 15:34 .
drwxr-xr-x  3 username  username  96  8 16 15:23 ..
-rw-r--r--  1 username  username   0  8 16 15:23 empty.txt
```

## 절대 경로 vs 상대 경로 관찰 메모
✅ $ pwd
/Users/username/codyssey/practice

✅ $ cd ..
✅ $ pwd
/Users/username/codyssey

절대 경로는 루트(Root) 디렉터리를 기점으로 전체 계층 구조를 모두 표기하는 방식으로, 내 로컬 환경에서는 실행 위치와 관계없이 항상 동일한 파일을 참조하므로 데이터 로드의 안정성이 높다. 반면, 상대 경로는 현재 작업 디렉터리(PWD)를 기준으로 .(현재)이나 ..(상위) 등의 축약어를 사용해 파일 위치를 지정한다. 프로그램을 타 장치에 배포하거나 이식할 때, 사용자마다 루트 이하의 디렉터리 구조나 사용자명이 다를 수 있다. 따라서 배포용 프로그램이나 프로젝트 내부 자원을 참조할 때는 애플리케이션 구조 내에서 유연하게 대응할 수 있는 상대 경로로 작성하는 것이 훨씬 안전하고 이식성이 높다.