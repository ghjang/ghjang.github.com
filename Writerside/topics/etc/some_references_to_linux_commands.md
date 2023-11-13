# 몇가지 참고할 만한 리눅스 커맨드

## '디렉토리 자체' 정보 출력

디렉토리내에 포함된 파일 또는 서브 디렉토리 정보가 아니라 '디렉토리 자체' 정보만을 출력하기 위해서 `ls` 명령어에 `-d` 옵션을 지정해 다음과 같이
사용할 수 있다. `-d` 옵션을 지정한 `ls` 명령어에 디렉토리가 아닌 '파일'이 지정된 경우에는 해당 파일의 정보가 그대로 출력된다.

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

특정한 디렉토리내의 디렉토리 정보만을 출력하기 위해서 `grep` 명령어를 파이프로 연결해서 다음과 같이 사용할 수도 있다. `ls -l` 출력 '행의 첫문자가 d'인
것만을 추출하는 방법이다.

```bash
ls -l | grep ^d
```

```bash
(base) ghjang@Gils-MacBook-Air Writerside % ls -l topics | grep ^d
drwxr-xr-x  5 ghjang  staff    160 Nov  4 20:38 computer_programming
drwxr-xr-x  4 ghjang  staff    128 Nov  6 10:42 music
```

'디렉토리 명'만을 출력하기 위해서 `awk` 명령어를 파이프로 연결해서 다음과 같이 사용할 수도 있다. `ls -l` 출력행의 '9번째 열'을 뽑아서 출력하는 방법이다.

```bash
ls -l | grep ^d | awk '{print $9}'
```

```bash
(base) ghjang@Gils-MacBook-Air Writerside % ls -l topics | grep ^d | awk '{print $9}'
computer_programming
music
```

마지막으로 '디렉토리의 총 개수'를 출력하기 위해서 `wc` 명령어를 파이프로 연결해서 다음과 같이 사용할 수도 있다. wc 명령어에 '줄'을 세도록 하는
`-l` 옵션을 지정했다.

```bash
ls -l | grep ^d | wc -l
```

```bash
(base) ghjang@Gils-MacBook-Air Writerside % ls -l topics | grep ^d | wc -l
       2
```

`*`와 같은 '와일드 카드' 문자가 포함된 `ls -l ~/.*` 명령어를 사용하면 사용자의 '홈 디렉토리'의 `.`으로 시작하는 '파일과 디렉토리'들이 '개별적'으로
`ls -l` 명령어에 전달된 것과 같은 결과를 출력한다. 그러니까 '와일드 카드 문자의 확장'이 '쉘'에서 먼저 발생하고 그 결과가 `ls -l` 명령어에 각각
전달된다. 따라서 `-d` 옵션을 지정하지 않고 `ls -l ~/.*` 명령어를 사용하면 '홈 디렉토리'의 `.`으로 시작하는 '디렉토리' 내의 정보까지 '모두'
리스팅된다. 보통은 `-d` 옵션을 지정해 `ls -ld ~/.*`와 같이 실행하는 것이 의도했던 것일 수도 있겠다.

아래에 몇가지 참고할만한 `ls` 명령어의 옵션에 대해서 사족을 붙였다.

`-a` 옵션은 '숨김 파일'을 포함해서 나열한다. '숨김 파일'은 파일명이 `.`으로 시작하는 파일을 말한다. `ls -a` 명령어를 사용하면 '숨김 파일'을 포함해서
나열된다. `ls -la` 명령어를 사용하면 '숨김 파일'을 포함해서 '상세 정보'까지 나열된다. 참고로 '현재 디렉토리'를 나타내는 `.`과 현재 디렉토리의
'부모 디렉토리'를 나타내는 `..`도 '숨김 파일'이어서 `-a` 옵션을 지정하지 않으면 나열되지 않는다.

`-F` 옵션은 파일과 디렉토리를 나열할 때, 각 항목을 식별하는데 좀더 도움을 준다. 다음과 같은 효과가 있다.

  1. **디렉토리에 슬래시(/) 표시**: 디렉토리의 경우 이름 뒤에 슬래시('/')를 붙여서 표시
  2. **실행 가능한 파일에 별표(*) 표시**: 실행 가능한 파일의 경우 이름 뒤에 별표(*)를 붙여서 표시
  3. **심볼릭 링크에 @ 표시**: 심볼릭 링크인 경우 이름 뒤에 '@'를 붙여서 표시

`ls -l` 명령어로 출력된 다소 많은 정보를 포함하는 결과에서 '접근 권한' 정보 부분을 통해서 '디렉토리'인지 '실행 가능한 파일'인지 '심볼릭 링크'인지
알 수 있지만, `-F` 옵션을 지정하면 좀더 명확하게 확인할 수 있다. `-l` 옵션을 빼고 `ls -F` 명령어를 사용해서도 나열된 '파일/디렉토리 이름 단수 목록'에서
'디렉토리'인지 '실행 가능한 파일'인지 '심볼릭 링크'인지 확인할 수 있다.

`ls -R` 명령어는 '하위 디렉토리'까지 '재귀적'으로 나열한다. `ls -t` 명령어는 '파일/디렉토리'를 '수정 시간'의 역순으로 나열한다. `ls -S` 명령어는
'파일/디렉토리'를 '크기'의 역순으로 나열한다.

## 빈 파일 생성 / 파일 수정 시간 변경

## 현재의 '쉘 세션'에서 사용하는 '환경 변수' 출력/설정/삭제

## 현재의 '쉘 세션'에서 '쉘 스크립트' 실행

## '프로세스 트리' 출력

## 특정 프로세스의 '환경 변수' 출력

## '.bash_profile / .bashrc' 파일

`~/.bashrc` 'run command' 스크립트 파일은 '터미널' 앱과 같은 사용자가 '명령어'를 직접 타이핑을 통해서 실행하는 '인터랙티브(non-login) 쉘'
실행 환경을 위한 것이다. 인터랙티브 쉘이 실행될때 마다 실행된다. 그러니까 '새로운 터미널 창'을 열 때마다 실행된다.

`~/.bash_profile` 파일은 '로그인 쉘'을 위한 스크립트 파일로 '새로 로그인'을 할때 마다 실행된다. `~/.bash_profile` 파일은 '로그인 쉘'에서만
실행되기 때문에 '새로운 터미널 창'을 열 때마다 실행되지는 않는다. '로그인 쉘'은 `~/bashrc` 파일을 자동으로 실행하지 않는다. 때문에 필요할 경우
`~/.bash_profile` 파일에서 `~/.bashrc` 파일을 실행하도록 설정해두는 것이 일반적이다. 다음과 같이 `~/.bash_profile` 파일에 스크립트를 추가해
`~/.bashrc` 파일을 실행하도록 설정할 수 있다.

```bash
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
```

여기서 한가지 주의할 점은 '로그인 쉘'이라는 것이 '터미널' 앱과 같은 '콘솔' 형태의 앱에서만 생성되는 것이 아니라는 것이다. 'GUI' 형태의 '운영체제'에서
'GUI 로그인 화면'을 통해서 시스템에 로그인할 때 내부적으로 암묵적으로 '어떤 로그인 쉘'이 실행되는 것이 보통이라는 것이다. 예를 들어서 '윈도, 리눅스,
맥OS'와 같이 'GUI 로그인 화면'을 제공하는 시스템에 로그인할 때 내부적으로 암묵적이게 '어떤 로그인 쉘'이 실행된다는 것이다.

'터미널' 앱에서 '원격 시스템'에 'telnet'이나 'ssh'로 로그인을 시도하면 '원격 시스템'에서 '로그인 쉘'을 실행한다. '원격 시스템'에서 '로그인 쉘'을
실행할 때 `~/.bash_profile` 파일이 실행되는 것이 보통이다. 원격 시스템에 터미널 형태로 사용자가 직접 또는 어떤 'CI 시스템'을 통해서 간접적으로
로그인시에 생성되는 '로그인 쉘'과 연계된 특정 'profile' 스크립트가 항상 실행되지 않을 수도 있는 시스템 또는 환경이 있다는 것 같다. 주의할 것.

어떤 '명령어'나 '환경 설정'을 `.bash_profile` 파일에서 진행할지 `.bashrc` 파일에 진행할지는 '로그인 쉘'과 '인터랙티브 쉘'의 차이를 이해하고
작업 환경에 따라서 결정하는 것이 맞겠다. '터미널 명령어 별칭 설정' 경우를 예로 들어보면, '맥OS'에서 'GUI' 형태로 로그인해 '터미널' 앱에서만 작업한다면
굳이 `.bash_profile` 파일에 별칭을 설정할 필요는 없겠다. 이 경우 `.bashrc` 파일에만 별칭을 두어도 될 것이다. 하지만 어떤 원격 시스템에 '터미널'
형태로 '원격 로그인'을 해서 작업해야하는 경우이고 '원격 시스템'에서는 `.bash_profile` 파일만 실행된다면 `.bash_profile` 파일에 별칭을 설정해두는
것이 더 맞는 상황일 수도 있겠다.
