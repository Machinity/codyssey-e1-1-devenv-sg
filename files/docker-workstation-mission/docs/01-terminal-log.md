# 01. 터미널 기본 조작 로그

요구된 항목: 현재 위치 확인, 목록 확인(숨김 파일 포함), 이동, 생성, 복사, 이동/이름변경, 삭제,
파일 내용 확인, 빈 파일 생성

아래 형식대로 **명령어 + 출력 결과**를 그대로 붙여넣으세요.

```bash
🔲 $ pwd
/Users/username/e1-1/codyssey-e1-1-devenv-sg

🔲 $ ls -la
total 24
drwxr-xr-x   6 username  username   192  8 16 15:18 .
drwxr-xr-x   3 username  username    96  8 16 15:05 ..
-rw-r--r--@  1 username  username  6148  8 16 15:11 .DS_Store
drwxr-xr-x  13 username  username   416  8 16 15:17 .git
drwx------@  6 username  username   192  8 16 15:18 files
-rw-r--r--   1 username  username    78  8 16 15:05 README.md

🔲 $ mkdir -p ~/codyssey/practice
🔲 $ cd ~/codyssey/practice

🔲 $ touch empty.txt
🔲 $ ls -la
total 0
drwxr-xr-x  3 username  username  96  8 16 15:23 .
drwxr-xr-x  3 username  username  96  8 16 15:23 ..
-rw-r--r--  1 username  username   0  8 16 15:23 empty.txt

🔲 $ cp empty.txt copy.txt
🔲 $ ls -la
total 0
drwxr-xr-x  4 username  username  128  8 16 15:33 .
drwxr-xr-x  3 username  username   96  8 16 15:23 ..
-rw-r--r--  1 username  username    0  8 16 15:33 copy.txt
-rw-r--r--  1 username  username    0  8 16 15:23 empty.txt

🔲 $ mv copy.txt renamed.txt
🔲 $ ls -la
total 0
drwxr-xr-x  4 username  username  128  8 16 15:33 .
drwxr-xr-x  3 username  username   96  8 16 15:23 ..
-rw-r--r--  1 username  username    0  8 16 15:23 empty.txt
-rw-r--r--  1 username  username    0  8 16 15:33 renamed.txt

🔲 $ echo "hello" > renamed.txt
🔲 $ cat renamed.txt
hello

🔲 $ rm renamed.txt
🔲 $ ls -la
total 0
drwxr-xr-x  3 username  username  96  8 16 15:34 .
drwxr-xr-x  3 username  username  96  8 16 15:23 ..
-rw-r--r--  1 username  username   0  8 16 15:23 empty.txt
```

## 절대 경로 vs 상대 경로 관찰 메모
🔲 $ pwd
/Users/username/codyssey/practice

🔲 $ cd ..
🔲 $ pwd
/Users/username/codyssey