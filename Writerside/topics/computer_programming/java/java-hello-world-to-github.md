# GitHub 저장소 업로드
작성된 '메이븐 멀티 모듈 프로젝트'를 'GitHub'에 업로드하기 위한 1가지 방법을 적는다.

## 1. GitHub 저장소 생성
먼저 GitHub 사이트에서 제공하는 화면을 이용해서 'hello_java_world'라는 이름의 저장소를 생성한다. 'README.md', 'LICENSE', '.gitignore'
파일을 생성하는 적절한 옵션을 선택후 진행하도록 한다.

## 2. 로컬 저장소 생성
터미널 앱에서 최상위 메이븐 프로젝트 'hello_java_world'의 'pom.xml' 파일이 있는 폴더로 이동한다. 그리고 아래와 같이 명령어들을 입력해 저장소를
초기화한다.

```bash
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/ghjang/hello_java_world.git
```

`git branch -M main`은 별다른 `git` 설정 변경을 하지 않고 `git init`시 전통적으로 생성되는 `master` 브랜치를 `main` 브랜치로 변경하는
명령어이다. `main`으로 변경하는 이유는 현재 GitHub에서 신규 저장소의 기본 생성 브랜치가 `main`으로 변경되었기 때문이다.

참고로 `git init`시에 생성되는 '기본 브랜치(default branch)'를 변경하기 위해서는 `git config --global init.defaultBranch main` 명령어를
실행하면 된다. `--global` 옵션은 '현재 사용자'의 전역 설정을 변경하겠다는 의미로 이후에 해당 사용자가 생성되는 모든 저장소의 기본 브랜치가 `main`으로
생성되게 한다.

`git remote add origin https://github.com/ghjang/hello_java_world.git` 명령어는 로컬 저장소와 원격 저장소를 연결하는 명령어이다. 여기서
`origin`은 로컬 저장소에서 원격 저장소를 가리키는 별칭이다. `https://github.com/ghjang/hello_java_world.git` HTTP URL은 이 로컬 저장소의
내용을 임포트시킬 신규로 생성한 'hello_java_world' GitHub 저장소의 주소이다. 이 명령어를 해석해보면 '원격 저장소 URL'을 'origin'이라는 별칭으로
'로컬 저장소'에 추가한다는 의미이다.

## 3. 원격 저장소의 내용 가져오기
이제 원격 저장소의 내용을 가져와서 로컬 저장소에 병합시키는 작업을 진행해야한다. 여기서 설명한 방식대로 진행했다면 원격 'hello_java_world' 저장소에는
'README.md', 'LICENSE', '.gitignore' 파일이 생성되어 있을 것이다. 이 파일들 아직 로컬 저장소에 없기 때문에 원격 저장소의 내용을 가져와서
병합시켜야한다.

```bash
git pull origin main --rebase
```

이 명령어는 원격 저장소의 `main` 브랜치의 내용을 가져와서 로컬 저장소의 `main` 브랜치에 병합시키는 명령어이다. `--rebase` 옵션은 병합시에
'rebase' 방식으로 병합하겠다는 의미이다. 아직까지 원격 저장소와 로컬 저장소간에 '공통 커밋'이 없기 때문에 단순히 `git pull origin main` 명령어를
실행하면 '발산 브랜치(divergent branch)' 관련 오류가 발생한다. 이 오류를 해결하기 위한 한가지 방법으로 여기서는 `--rebase` 옵션을 사용했다.

참고로 최초에 신규 'hello_java_world' GitHub 저장소를 생성할 때 'README.md', 'LICENSE', '.gitignore' 파일을 생성하는 옵션을 선택하지 않고
빈 원격 저장소를 생성했다면 `git pull origin main --rebase` 명령어를 실행할 필요는 없다.

`git pull` 명령어 실행이 완료되면 로컬 저장소에 'README.md', 'LICENSE', '.gitignore' 파일이 생성된 것을 확인할 수 있다. 필요하다면 이 파일들을
적절히 수정하고 커밋하면 된다.

## 4. 로컬 저장소의 내용 원격 저장소에 업로드
마지막으로 로컬 저장소의 내용을 원격 저장소에 업로드하는 작업을 진행한다. 다음과 같은 명령어를 실행한다.

```bash
git push -u origin main
```

이 명령어는 로컬 저장소의 `main` 브랜치의 내용을 원격 저장소의 `main` 브랜치에 업로드한다는 의미이다. `-u` 옵션은 아직 로컬 저장소로부터 원격 저장소로
업로드된 적이 없기 때문에 이 명령어를 실행하면 '업스트림(upstream) 브랜치' 설정을 하겠다는 의미이다. 이 설정을 하면 이후에 `git push` 명령어를 실행할
때 `-u` 옵션을 생략할 수 있다.

이제 GitHub 사이트에서 'hello_java_world' 저장소의 내용을 확인해보면 로컬 저장소의 내용이 업로드된 것을 확인할 수 있을 것이다.
