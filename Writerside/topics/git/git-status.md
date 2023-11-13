# '저장소 상태' 확인(git status)

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
