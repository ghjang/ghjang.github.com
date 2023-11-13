# 'Unstaged, Modified' 상태의 파일을 '스테이징' 단계 없이 곧바로 '커밋'하기

다음과 같이 '-a' 옵션을 주어 'git commit' 명령어를 실행하면 'Staged, Modified' 단계로 상태를 전이시키지 않고 곧바로 '커밋'을 수행한다.
```bash
git commit -a
```

다음과 같이 '-m' 옵션을 추가로 지정해서 기존처럼 'core.editor' 설정에 지정된 에디터를 구동하여 커밋 메시지를 편집하지 않고 곧바로 커밋 메시지를
지정해 커밋할 수도 있다.

```bash
git commit -a -m "commit message"
```
