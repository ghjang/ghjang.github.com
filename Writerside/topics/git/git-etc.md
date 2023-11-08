# 기타 참고사항

1. 'working folder'에서 'Tracked' 상태의 파일을 'rm'으로 삭제후 'git pull' 명령어를 실행해도 삭제된 파일이 다시 '복구'되지 않는다.<br/><br/>
   'SVN'의 경우는 'working folder'에서 파일을 'rm'으로 삭제후 'svn update' 명령어를 실행하면 삭제된 파일이 다시 '복구'된다.<br/><br/>
   'git'의 경우는 'working folder'에서 'Tracked' 상태의 파일을 'rm'으로 삭제시 'Deleted' 상태로 전이될 뿐이다. 'Deleted' 상태의 파일은
   'git restore / git restore --staged' 명령어를 이용해서 복구할 수 있다. 이글 상단의 '파일 상태 전이' 그림을 참조할 것.

2. 'git rm -f'를 'Staged, Added' 상태의 파일에 대해서 실행하면 'Deleted' 상태 전이 없이 곧바로 파일이 'working folder'에서 삭제된다.<br/><br/>
   'Added' 상태로 전이후 해당 파일에 대해서 커밋이 수행된 적이 없기 때문에 'git rm -f' 명령어 실행시 별다르게 '삭제 자체' 관련 기록을 유지할 필요가 없다는
   상황으로 보인다. 'Staged, Modified' 상태와 'Unstaged, Unmodified/Modified' 상태의 파일에 대해서 'git rm -f' 명령어를 실행하면
   'Staged, Deleted' 상태로 전이되고 파일이 'working folder'에서 삭제된다. (이 참고사항의 내용을 이 글 상단의 '파일 상태 전이' 그림에 마저 추가할
   수도 있었으나, 이미 그림 자체가 너무 복잡해져서 그렇게 하지 않았다.)

3. '동일한 동작'을 수행하는 서로 다른 'git verb' 명령어들이 있다.<br/><br/>
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

4. 어떤 git 명령어 옵션은 '축약형'의 등가 옵션이 같이 제공된다.<br/><br/>
   아래와 같은 식이다.
    * 브랜치 이름 변경
      ```bash
      git branch --move --force <new branch name>
      ```

      ```bash
      git branch -M <new branch name>
      ```
      'git branch'에 사용된 옵션이 의미하는 것은 '현재 참조하고 있는 로컬 작업 브랜치'를 '강제(force)'로 'new branch name'으로
      '이동(move)' 시킨다는 것이다. 추가적으로 'new branch name' 브랜치가 현재 참조하는 로컬 작업 브랜치로 설정된다.

5. 어떤 'git verb' 명령어에서 '--staged'와 '--cached'는 동일한 의미로 사용된다.<br/><br/>
   즉 'git verb --staged'와 'git verb --cached'는 동일한 동작을 수행하는 명령어도 있다는 것이다.
   예를 들어서 다음의 두 명령어는 동일한 동작을 수행한다. 모든 명령어에서 그런 것은 아니니 주의할 필요가 있다.

    * 'staging area'에 추가된 파일 변경사항을 '직전 커밋' 파일과 비교
       ```bash
       git diff --staged
       ```

       ```bash
       git diff --cached
       ```

6. 'git diff'와 같은 명령어에서 출력하는 'diff 포맷'은 'patch 포맷'이다.
   다음과 같이 해당 내용을 해석할 수 있다.

7. 'gitk, git-gui'와 같은 'GUI' 형태의 '기본 저장소 브라우저 앱'이 있다.<br/><br/>
   물론 다른 'GUI' 기반의 '저장소 브라우저'도 있다. 'gitk'와 'git gui'는 'git' 패키지에 포함되어 있는 'GUI' 기반의 '저장소 브라우저'이다.<br/><br/>
   macOS에서는 다음과 같이 설치할 수 있다.
    ```bash
    brew update
    brew install git
    brew install git-gui
    ```
   설치후 'git 저장소' 폴더내에서 'gitk' 명령어를 실행하면 'GUI' 형태의 '저장소 브라우저' 앱이 실행되고 해당 git 저장소의 내용이 표시된다.
   참고로 'File > start git gui' 메뉴를 통해서 'git gui'를 별도로 실행할 수 있다. 이 앱들의 자세한 사용법에 대해서는
   [gitk](https://git-scm.com/docs/gitk)와 [git-gui](https://git-scm.com/docs/git-gui) 공식 문서를 참고할 수 있다.

