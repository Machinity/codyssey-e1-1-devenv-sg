# 02. 파일 권한 실습 로그

최소 요구: **파일 1개 + 디렉터리 1개**에 대해 권한 변경 전/후를 비교합니다.

## 파일 권한 변경

```bash
🔲 $ touch script.sh
🔲 $ ls -l script.sh
-rw-r--r--  1 username  username  0  8 16 15:37 script.sh

🔲 $ chmod 755 script.sh
🔲 $ ls -l script.sh
-rwxr-xr-x  1 username  username  0  8 16 15:37 script.sh
```

## 디렉터리 권한 변경

```bash
🔲 $ mkdir private_dir
🔲 $ ls -ld private_dir
drwxr-xr-x  2 username  username  64  8 16 15:38 private_dir

🔲 $ chmod 700 private_dir
🔲 $ ls -ld private_dir
drwx------  2 username  username  64  8 16 15:38 private_dir
```

## 권한 표기 해석 메모
🔲 755 = 소유자 rwx / 그룹 r-x / 기타 r-x
🔲 644 = 소유자 rw- / 그룹 r-- / 기타 r--.
🔲 숫자는 r=4, w=2, x=1의 합
