# Git

여기서는 혼동되거나 따로 정리할 필요가 있다고 생각되는 git 사용법에 대해서 일부 정리한다.
git에 대한 일반적인 사용법에 대해서는 아래 학습자료를 참고해볼 수 있다.

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
> [git-scm.com](https://git-scm.com) 사이트에서 무료로 배포하는 '오픈소스 책'이 있다. '1 ~ 3'장은 git의 기본 개념과 일상 사용법에 대해서 설명하고 있다.
> 3장까지 읽어보면 git 클라이언트 사용자 측면에서 git의 기본 개념과 사용법에 대해서 충분히 이해할 수 있다.
>
{style="note"}

## 로컬 git 저장소 작업 폴더내 파일 상태 전이

모든 '파일 상태'와 완전한 '상태 전이'를 기록한 것은 아니지만, 우선 다음의 'git 클라이언트 사용 흐름' 그림을 참고해 시작할 수 있다.

![로컬 git 저장소 작업 폴더내 파일 상태 전이도](git_file_state_diagram.png){ width=1024 }

- 'git rm --cached' 명령어와 관련해 이 글의 **'Tracked' 상태의 파일 'Untracked' 상태로 변경하기** 섹션의 내용 참조할 것.
- 'git rm -f' 명령어와 관련해 이 글의 **[기타 참고사항](git-etc.md)** 페이지 참조할 것.
- 'git commit -a' 명령어와 관련해 이 글의 **'Unstaged, Modified' 상태의 파일을 '스테이징' 단계 없이 곧바로 '커밋'하기** 섹션의 내용 참조할 것.
