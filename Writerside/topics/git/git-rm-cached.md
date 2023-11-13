# 'Tracked' 상태의 파일 'Untracked' 상태로 변경

'working directory'에 파일은 유지시키고 'Tracked' 상태의 파일을 'Untracked' 상태로 변경하고자 할 때 다음 명령어를 사용한다.
[Git](_Git.md) 페이지내 '파일 상태 전이' 그림에서 'File Existing' 상태내의 파일에 대해서 이 명령어를 실행시킬 수 있다.
'Deleted' 상태의 파일에서는 실행시킬 수 없다.

```bash
git rm --cached <file name>
```

명령어 실행후 최종적으로 해당 파일을 'staging area'에서 제거하기 위해서 'git commit' 명령어를 실행해야 한다.

결과적으로 'git rm'이나 'rm'과는 'working directory'에 있는 파일은 삭제하지 않는다는 점 정도가 다른 차이점이다.
