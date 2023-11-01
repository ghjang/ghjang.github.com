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

## 로컬 git 저장소 작업 폴더내 파일 상태 전이

완전한 상태 전이를 기록한 것은 아니지만 다음의 그림을 우선 참고할 수 있다.

![로컬 git 저장소 작업 폴더내 파일 상태 전이도](git_file_state_diagram.png){ width=1024 }

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

## '스테이징' 단계 없이 곧바로 '커밋'하기

다음과 같이 '-a' 옵션을 주어 'git commit' 명령어를 실행하면 'Staged' 단계로 상태를 전이시키지 않고 곧바로 '커밋'을 수행한다.
```bash
git commit -a
```

다음과 같이 '-m' 옵션을 추가로 지정해서 기존처럼 'core.editor' 설정에 지정된 에디터를 구동하여 커밋 메시지를 편집하지 않고 곧바로 커밋 메시지를
지정해 커밋할 수도 있다.

```bash
git commit -a -m "commit message"
```

## '삭제' 커밋된 파일 복구하기

## 'merge' 동작을 함축하는 'git verb'들

## 리베이싱

## 기타 참고사항

1. '동일한 동작'을 수행하는 서로 다른 'git verb' 명령어들이 있다.
현재 git의 버전은 '2.x'대 이다. 초기 git이 만들어진 이후에 진화하면서 새로운 'git verb' 명령어들이 추가되었다.
새로 추가된 명령어들 중에 어떤 명령어는 기존에 다른 명령어에서 수행할 수 있는 기능을 수행하지만 좀더 상황에 맞는 이름의 'verb'가 부여되었다.
예를 들어서 다음의 두 명령어는 동일한 동작을 수행한다.

   * '작업 브랜치' 변경
      ```bash
      git checkout <branch name>
      ```
      
      ```bash
      git switch <branch name>
      ```
     
2. 어떤 'git verb' 명령어에서 '--staged'와 '--cached'는 동일한 의미로 사용된다.
즉 'git verb --staged'와 'git verb --cached'는 동일한 동작을 수행한다. 예를 들어서 다음의 두 명령어는 동일한 동작을 수행한다.

   * 'working directory'내 파일 변경사항을 'staging area'에 추가된 변경사항과 비교,
     'staging area'에 추가된 변경사항이 없을 경우에는 '직전 커밋'된 파일과 비교
      ```bash
      git diff --staged
      ```
   
      ```bash
      git diff --cached
      ```

## How to perform a task

Some introductory information.

1. Step with a code block
   ```bash
    run this --that
   ```
   
2. Step with an image
   ![]()

<!-- The 'src' attribute should contain the name of an image from the '/images' folder in your project -->
