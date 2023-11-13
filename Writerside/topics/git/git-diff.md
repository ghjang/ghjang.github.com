# '파일 변경사항' 확인(git diff)

실전에서는 'Visual Studio Code'나 'JetBrains'사의 툴들 처럼 GUI로 제공되는 'diff' 기능을 사용해 작업하게되겠지만,
git 명령어를 통해서 확인하는 방법을 알아두는 것도 나쁘지 않다.

```bash
git diff
```
별다른 옵션없이 'git diff' 명령어를 실행하면 'working directory'와 'staging area'에 추가된 파일 변경사항을 비교해서 출력해준다.
'staging area'에 추가된 변경사항이 없을 경우에는 'working directory'와 '직전 커밋' 파일 변경사항을 비교해서 출력해준다.
참고로 'working directory'의 내용을 'git add'를 통해서 'staging area'에 추가한 직후에 'git diff' 명령어를 실행하면
'working directory'와 'staging area'에 추가된 변경사항 사이에 차이가 없음으로 아무것도 출력되지 않는다.

```bash
git diff --staged
```
'staging area'에 추가된 변경사항과 '직전 커밋' 파일 변경사항을 비교해서 출력해준다.

```bash
git difftool
```
'git difftool' 명령어를 실행하면 'git diff' 명령어를 실행한 결과를 'git config' 명령어를 통해서 설정된 'diff.tool'에 지정된 'diff tool'을
구동시켜서 변경사항을 확인할 수 있게 해준다. 'git config' 명령어를 통해서 'diff.tool'을 설정해두지 않은 상태에서 'git difftool' 명령어를 실행하면
다음과 같은 안내 메시지와 시스템에서 이용 가능한 'diff tool'의 실행 여부를 확인하는 메시지가 출력된다.

```bash
$ git difftool

This message is displayed because 'diff.tool' is not configured.
See 'git difftool --tool-help' or 'git help config' for more details.
'git difftool' will now attempt to use one of the following tools:
opendiff kompare emerge vimdiff nvimdiff

Viewing (1/1): 'Writerside/topics/Git.md'
Launch 'opendiff' [Y/n]? 
```

위 예시 출력에서는 'opendiff'라는 'diff tool'을 구동시키겠다는 메시지가 출력되었다.
'Y'를 입력하면 'opendiff'가 구동되어 변경사항을 확인할 수 있다. 'N'을 입력하면 'git difftool' 명령어 실행이 종료된다.
