# Git

여기서는 혼동되거나 따로 정리할 필요가 있다고 생각되는 git 사용법에 대해서 일부 정리한다. git에 대한 일반적인 사용법에 대해서는 아래 학습자료를 참고해볼 수 있다.

> **git 사용법 인터랙트 학습 사이트: [Learn Git Branching](https://learngitbranching.js.org)**
>
> '게임'을 하는 것처럼 git을 학습할 수 있는 사이트이다.
> 
> 'git add' 명령어를 이용해 'staging' 하는 과정이 없다는 것만을 제대로 인식하고 내용을 살펴보면 된다.
> 
> git의 'commit tree'를 조작하는 상황을 '애니메이션 시각화'를 했기 때문에 이해에 꽤 도움이 된다.
>
{style="note"}

> **git 사용법 무료 책: [Pro Git](https://git-scm.com/book/en/v2)**
>
> [git-scm.com](https://git-scm.com) 사이트에서 무료로 배포하는 '오픈소스 책'이 있다.
>
{style="note"}

## 몇가지 꼭 기억해야할 사항

1. git은 '분산 버전 관리 시스템(DVCS)'이다.

## 로컬 git 저장소 작업 폴더내 파일 상태 전이

모든 '파일 상태'와 완전한 '상태 전이'를 기록한 것은 아니지만 우선 다음의 그림을 참고해 시작할 수 있다.

![로컬 git 저장소 작업 폴더내 파일 상태 전이도](git_file_state_diagram.png){ width=1024 }

- 'git rm --cached' 명령어와 관련해 이 글의 **'Tracked' 상태의 파일 'Untracked' 상태로 변경하기** 섹션의 내용 참조할 것.
- 'git rm -f' 명령어와 관련해 이 글의 **기타 참고사항** 섹션의 **1번** 내용 참조할 것.
- 'git commit -a' 명령어와 관련해 이 글의 **'Unstaged, Modified' 상태의 파일을 '스테이징' 단계 없이 곧바로 '커밋'하기** 섹션의 내용 참조할 것.

## '저장소 상태' 파악하기

```bash
git status
```
저장소 상태에 대해서 문장으로 다소 많은 양의 설명을 포함해 출력해주는 명령어이다.

```bash
$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Writerside/topics/Git.md

no changes added to commit (use "git add" and/or "git commit -a")
```
저장소 상태에 대한 설명과 함께 각 상태에서 다른 상태로 전이하기 위한 명령어 정보를 같이 포함해 출력해주고 있다.

```bash
git status -s
```
'-s' 옵션은 '간단한 형태(short format)'로 저장소 상태를 출력해준다.

```bash
$ git status -s
MM Writerside/topics/Git.md
```
우선 저장소에 아무런 변경사항이 없을 경우에는 아무것도 출력되지 않는다.

변경사항이 존재할 경우에는 위와 같이 변경된 파일에 대한 상태를 표시해준다. 예시 출력에서는 1개의 'Git.md' 파일에 대해서 'MM'으로 표시되어 있는데
이는 'staging area'에 추가되어 'Modified'된 상태에 있고, 'working directory'에서도 'Modified'된 상태라는 것을 의미한다.
'MM'의 두글자 표현에서 왼쪽 글자는 'staging area'에 대한 상태를, 오른쪽 글자는 'working directory'에 대한 상태를 나타낸다.

'staging area' 상태 표시 부분에 가능한 값은 '공백문자', 'A', 'M', 'D', 'R', 'C', 'U' 그리고 '?'가 있다.
'working directory' 상태 표시 부분에 가능한 값은 '공백문자', 'M', 'D', 'R', 'C', 'U' 그리고 '?'가 있다.

'공백문자'는 해당 영역에서 변경사항이 없다는 것을 의미한다. 'staging area'와 'working directory' 영역 모두에서 변경사항이 없는 파일의 경우에 
이 명령어는 해당 파일에 대해서 2개의 공백문자를 표시하는게 아니라 아예 출력에서 배제시킨다. 

'A'는 'Added'를 의미하고, 'M'은 'Modified'를 의미한다. 'D'는 'Deleted'를 의미하고, 'R'은 'Renamed'를 의미한다.

'?'는 'Untracked'를 의미한다. 'Untracked'는 git에 의해서 'Tracking'되지 않고 'working directory'에만 존재하는 파일의 상태이다.
Untracked 상태의 파일이 존재할 경우 이 명령어는 해당 파일에 대해서 '??'로 표시해준다. '??'가 아닌 다른 상태 문자로 표시되는 다른 파일들은 'Tracked'
상태의 파일들이다. '.gitignore' 파일에 지정된 패턴의 파일들은 'Untracked' 상태의 파일이지만 이 명령어의 출력에서 사전에 배제되어 표시되지 않는다.

## '파일 변경사항' 확인하기

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

## 'Unstaged, Modified' 상태의 파일을 '스테이징' 단계 없이 곧바로 '커밋'하기

다음과 같이 '-a' 옵션을 주어 'git commit' 명령어를 실행하면 'Staged, Modified' 단계로 상태를 전이시키지 않고 곧바로 '커밋'을 수행한다.
```bash
git commit -a
```

다음과 같이 '-m' 옵션을 추가로 지정해서 기존처럼 'core.editor' 설정에 지정된 에디터를 구동하여 커밋 메시지를 편집하지 않고 곧바로 커밋 메시지를
지정해 커밋할 수도 있다.

```bash
git commit -a -m "commit message"
```

## 'Tracked' 상태의 파일 'Untracked' 상태로 변경하기

'working directory'에 파일은 유지시키고 'Tracked' 상태의 파일을 'Untracked' 상태로 변경하고자 할 때 다음 명령어를 사용한다.
이 글 상단의 '파일 상태 전이' 그림에서 'File Existing' 상태내의 파일에 대해서 이 명령어를 실행시킬 수 있다.
'Deleted' 상태의 파일에서는 실행시킬 수 없다.

```bash
git rm --cached <file name>
```

명령어 실행후 최종적으로 해당 파일을 'staging area'에서 제거하기 위해서 'git commit' 명령어를 실행해야 한다.

결과적으로 'git rm'이나 'rm'과는 'working directory'에 있는 파일은 삭제하지 않는다는 점 정도가 다른 차이점이다.

## '삭제' 커밋된 파일 복구하기

'git commit'으로 일단 커밋된 내용은 이후에 '삭제'등의 명령어 실행 후 추가 커밋을 하더라도 '거의' 모두 복구가 가능하다고 한다.

## 커밋된 파일 '완전 삭제'하기

## 'merge' 동작을 함축하는 'git verb'들

## 리베이싱

## 기타 참고사항

1. 'git rm -f'를 'Staged, Added' 상태의 파일에 대해서 실행하면 'Deleted' 상태 전이 없이 곧바로 파일이 'working folder'에서 삭제된다.<br/><br/>
'Added' 상태로 전이후 해당 파일에 대해서 커밋이 수행된 적이 없기 때문에 'git rm -f' 명령어 실행시 별다르게 '삭제 자체' 관련 기록을 유지할 필요가 없다는
상황으로 보인다. 'Staged, Modified' 상태와 'Unstaged, Unmodified/Modified' 상태의 파일에 대해서 'git rm -f' 명령어를 실행하면 
'Staged, Deleted' 상태로 전이되고 파일이 'working folder'에서 삭제된다. (이 참고사항의 내용을 이 글 상단의 '파일 상태 전이' 그림에 마저 추가할
수도 있었으나, 이미 그림 자체가 너무 복잡해져서 그렇게 하지 않았다.)

2. '동일한 동작'을 수행하는 서로 다른 'git verb' 명령어들이 있다.<br/><br/>
현재 git의 버전은 '2.x'대 이다. 초기 git이 만들어진 이후에 진화하면서 새로운 'git verb' 명령어들이 추가되었다.
특히 'git reset'과 'git checkout'은 많은 기능을 수행할 수 있는 명령어로 다소 직관적이지 않은 사용법들이 있다.
새로 추가된 명령어들 중에 어떤 명령어는 기존에 다른 명령어에서 수행할 수 있는 동일 기능을 수행하지만 좀더 상황에 맞는 이름의 'verb'가 부여되었다.
예를 들어서 다음의 명령어들은 동일한 동작을 수행한다.

   * '작업 브랜치' 변경
      ```bash
      git checkout <branch name>
      ```
      
      ```bash
      git switch <branch name>
      ``` 
     
   * 'Added' 상태로 'staging area'에 추가되어 'Tracked' 상태인 파일을 'Untracked' 상태로 변경
      ```bash
      git rm --cached <file name>
      ```
     
      ```bash
      git restore --staged <file name>
      ```

3. 어떤 'git verb' 명령어에서 '--staged'와 '--cached'는 동일한 의미로 사용된다.<br/><br/>
즉 'git verb --staged'와 'git verb --cached'는 동일한 동작을 수행하는 명령어도 있다는 것이다. 
예를 들어서 다음의 두 명령어는 동일한 동작을 수행한다. 모든 명령어에서 그런 것은 아니니 주의할 필요가 있다.

   * 'staging area'에 추가된 파일 변경사항을 '직전 커밋' 파일과 비교
      ```bash
      git diff --staged
      ```
   
      ```bash
      git diff --cached
      ```
     
4. 'git diff'와 같은 명령어에서 출력하는 'diff 포맷'은 'patch 포맷'이다. 
다음과 같이 해당 내용을 해석할 수 있다.

## How to perform a task

Some introductory information.

1. Step with a code block
   ```bash
    run this --that
   ```
   
2. Step with an image
   ![]()
