# macOS에서 참고할 만한 커맨드

## 'Application' 앱 실행

```bash
open -a <application name>
```

## brew 자체 업데이트

macOS의 패키지 매니저 'brew 자체'를 업데이트하기 위해서 '터미널' 앱에서 다음과 같이 명령어를 실행할 수 있다.
참고로 '쉘'에서 '&&'은 '성공시에만 다음 실행'을 의미한다.

```bash
brew update && brew upgrade && brew cleanup
```
