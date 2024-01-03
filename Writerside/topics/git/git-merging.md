# 저장소 내용 병합

## '병합(merge)' 동작을 함축하는 'git verb'들
`git merge` 명령어가 아닌 다른 명령어들도 '병합(merge)' 동작을 함축하는 'git verb'들이 있다.

`git pull origin main --allow-unrelated-histories` 명령어는 '로컬 저장소'와 '원격 저장소'간에 '공통 커밋'이 없는 상황에서 '원격 저장소'의
`main` 브랜치의 내용을 '로컬 저장소'의 `main` 브랜치에 병합시킨다.

`git pull origin main --rebase`

## 전혀 연관이 없는 저장소 내용 병합
TODO: 가능?? 가능하다면 어떻게??