# 몇가지 참고할 만한 리눅스 커맨드

## '디렉토리 자체' 정보 출력

디렉토리내에 포함된 파일 정보가 아니라 '디렉토리 자체' 정보만을 출력하기 위해서 'ls' 명령어에 'd' 옵션을 지정해 다음과 같이 사용할 수 있다.

```bash
ls -ld <디렉토리 경로>
```

```bash
(base) ghjang@Gils-MacBook-Air Writerside % ls -al
total 48
drwxr-xr-x  10 ghjang  staff   320 Nov  6 16:43 .
drwxr-xr-x  15 ghjang  staff   480 Nov  2 09:51 ..
-rw-r--r--@  1 ghjang  staff  6148 Nov  6 09:55 .DS_Store
-rw-r--r--   1 ghjang  staff   233 Oct 23 14:29 c.list
drwxr-xr-x   3 ghjang  staff    96 Oct 23 14:29 cfg
-rw-r--r--   1 ghjang  staff  1722 Nov  6 16:43 gb.tree
drwxr-xr-x  12 ghjang  staff   384 Nov  6 10:10 images
drwxr-xr-x  10 ghjang  staff   320 Nov  6 16:41 topics
-rw-r--r--   1 ghjang  staff   180 Oct 23 14:29 v.list
-rw-r--r--   1 ghjang  staff   265 Oct 23 14:40 writerside.cfg

(base) ghjang@Gils-MacBook-Air Writerside % ls -l topics
total 56
-rw-r--r--  1 ghjang  staff  11663 Nov  6 14:20 Git.md
-rw-r--r--  1 ghjang  staff   1188 Oct 24 10:52 Tutorial.md
-rw-r--r--  1 ghjang  staff    234 Nov  6 16:35 brew-자체-업데이트.md
drwxr-xr-x  5 ghjang  staff    160 Nov  4 20:38 computer_programming
-rw-r--r--  1 ghjang  staff      7 Nov  4 20:36 etc.md
drwxr-xr-x  4 ghjang  staff    128 Nov  6 10:42 music
-rw-r--r--  1 ghjang  staff   1038 Nov  6 16:49 몇가지-참고할만한-리눅스-커맨드.md

(base) ghjang@Gils-MacBook-Air Writerside % ls -ld topics
drwxr-xr-x  10 ghjang  staff  320 Nov  6 16:46 topics
```

특정한 디렉토리내의 디렉토리 정보만을 출력하기 위해서 'grep' 명령어를 파이프로 연결해서 다음과 같이 사용할 수도 있다. 'ls -l' 출력 '행의 첫문자가 d'인
것만을 추출하는 방법이다.

```bash
ls -l | grep ^d
```

```bash
(base) ghjang@Gils-MacBook-Air Writerside % ls -l topics | grep ^d
drwxr-xr-x  5 ghjang  staff    160 Nov  4 20:38 computer_programming
drwxr-xr-x  4 ghjang  staff    128 Nov  6 10:42 music
```

'디렉토리 명'만을 출력하기 위해서 'awk' 명령어를 파이프로 연결해서 다음과 같이 사용할 수도 있다. 'ls -l' 출력행의 '9번째 열'을 뽑아서 출력하는 방법이다.

```bash
ls -l | grep ^d | awk '{print $9}'
```

```bash
(base) ghjang@Gils-MacBook-Air Writerside % ls -l topics | grep ^d | awk '{print $9}'
computer_programming
music
```

마지막으로 '디렉토리의 총 개수'를 출력하기 위해서 'wc' 명령어를 파이프로 연결해서 다음과 같이 사용할 수도 있다. wc 명령어에 '줄'을 세도록 하는
'-l' 옵션을 지정했다.

```bash
ls -l | grep ^d | wc -l
```

```bash
(base) ghjang@Gils-MacBook-Air Writerside % ls -l topics | grep ^d | wc -l
       2
```