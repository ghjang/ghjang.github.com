# 개발 환경 구성

## 1. JDK 설치

JDK는 'Oracle'에서 제공하는 것과 'OpenJDK'가 있다. OpenJDK의 경우 다양한 배포판이 존재한다. 꼭 공식 OpenJDK 사이트의 배포판을 사용할 필요는 없다.

'.exe'나 '.msi'과 같은 '인스톨러'를 사용하는 것보다 '.zip'이나 '.tar.gz'와 같은 압축 파일 형태로 '포터블'하게 배포되는 것을 사용하는 것이 좋다.
포터블하게 설치하면 여러 버전의 JDK를 설치할 수 있고, 설치된 JDK를 삭제할 때도 간단하다.

포터블하게 설치한 경우 적어도 `JAVA_HOME` 환경 변수를 설정해야 한다. `JAVA_HOME` 환경 변수는 JDK가 설치된 디렉토리를 가리킨다. 이 환경 변수는
다음에 설명할 '자바 IDE'를 포함해서 각종 자바관련 툴들에서 참조한다.

`JAVA_HOME` 환경 변수를 설정을 '사용자 레벨' 또는 '시스템 레벨'에서 설정할 수도 있지만 좀더 포터블하게 사용하기 위해서 '스크립트' 파일에서 설정하는
방식을 취할 수도 있다. '윈도OS'에서 예를 들자면 다음과 같은 내용을 포함하는 '.cmd' 파일을 만들어서 사용할 수 있다.

```
REM JAVA_HOME 설정
set JAVA_HOME=C:\Program Files\Java\jdk-11.0.2
 
REM PATH 환경 변수 업데이트,
REM (NOTE: Writerside 버그로 인해 환경 변수 참조 문자 `%` 사이에 '공백' 문자를 추가함.) 
set PATH=%PATH %;%JAVA_HOME %\bin

REM 설정한 'java' 정보 확인
where java
java -version

REM 콘솔창 자체 블럭킹없이,
REM 설정한 `JAVA_HOME` 환경 변수 정보를 '상속'받는 '자식 프로세스'를 실행
start "실행 파일 경로"
```

## 2. IDE 설치

보통 많이 사용하는 IDE는 'Eclipse'와 'IntelliJ IDEA'가 있다. 이 중에서 'IntelliJ IDEA'를 사용하는 것을 권장한다. 'IntelliJ IDEA'는
'Community Edition'과 'Ultimate Edition'이 있다. 'Ultimate Edition'은 유료이고 'Community Edition'은 무료이다. 'Eclipse'도
무료이다.

'Eclipse' 설치도 '인스톨러'를 사용하는 것보다 '포터블'하게 하는 것이 좋다. 'Eclipse'를 사이트에서 'JSP, Servlet'등의 개발 지원이 포함된
'Eclipse IDE for Enterprise Java Developers' 버전을 찾은 후 '압축파일' 형태로 다운로드 받은 후 압축을 풀어서 사용하면 된다. 실행전에
`JAVA_HOME` 환경 변수를 적절히 설정해야 한다. 먼저 설명한 '1. JDK 설치'에서 다룬 예제 '스크립트' 내용과 같은 식으로 적당한 '스크립트 파일'을 작성 후
IDE를 실행하는 방식을 취할 수도 있다.

사용하려는 IDE에서 개발에 필요한 특정 '프레임워크', 예를 들어서 'Java Spring Framework'를 이용한 개발을 지원하는지 확인이 필요하다. 기본적으로 지원을
포함할 수도 있고 별도의 '플러그인'을 설치해야 할 수도 있다. 'IntelliJ IDEA'의 경우 'Ultimate Edition'에서는 'Spring Framework'를 지원하지만
'Community Edition'에서는 지원하지 않는다. 'Eclipse'의 경우 'Spring Tool Suite'라는 별도의 '플러그인'을 설치해야 한다. 참고로 사용하려는
'Eclipse' 버전에 따라서 설치 가능한 'Spring Framework' 지원 플러그인이 다를 수 있다.

'eGovFrame'과 같은 'Spring Framework' 개발 지원을 포함하는 '커스터마이징'된 'Eclipse' 기반 IDE들도 존재한다. 작업 상황에 따라서 적절한 IDE를
선택하면 된다.

'Eclipse' 계열의 IDE를 사용하기로 결정했다면 'Window > Preferences' 메뉴를 선택한 후 'General > Workspace'에서 'Text file encoding'을
'UTF-8'로 설정하는 것을 권장한다. 'UTF-8'로 설정하지 않으면 'UTF-8'로 인코딩된 파일을 수정할 때마다 '인코딩'을 지정해야 하는 불편함이 있다.
'encoding'으로 'Preferences' 윈도의 설정 내용을 필터링 후 '에디터'등에서 필요시 추가로 'UTF-8'을 지정해야할 필요가 있을 수 있다.

## 3. 빌드 툴 설치

자바 프로젝트를 빌드하기 위해 사용할 수 있는 빌드 툴로 'Maven(메이븐)'과 'Gradle(그래들)'이 있다. '메이븐'이 더 먼저 나왔고 '그래들'은 '메이븐'의
단점을 보완해 나중에 나온 것이다. '메이븐'은 프로젝트 빌드 설정에 'XML'을 사용하고 '그래들'은 'Groovy'를 사용한다. '메이븐'은 여전히 널리 사용되고 있어
알아둘 필요가 있다.

보통 메이븐을 지원하는 IDE를 설치했다면 이미 메이븐 툴이 IDE에 내장된 것으로 별도로 메이븐을 설치할 필요가 없다. 해당 IDE의 메이븐 관련 기능 GUI 메뉴등을
찾아서 이용하면 된다. 하지만 IDE에서가 아닌 어떤 'CI' 환경에서 메이븐을 통해서 빌드를 할 필요가 있다거나, 메이븐 커맨드 `mvn`을 직접 실행할 필요가 있는
상황이라면 별도로 설치를 해주어야 할 수도 있다.

메이븐의 설치는 메이븐 공식 사이트인 '<https://maven.apache.org>'에서 '압축파일' 형태로 제공되는 '포터블 바이너리' 버전을 다운로드 받아서 사용하면
된다. 압축을 풀고 나면 `bin` 폴더 하위에 `mvn` 커맨드가 존재한다. `mvn` 커맨드를 실행하기 전에 `JAVA_HOME` 환경 변수를 적절히 설정해주어야 한다.
필요하다면 `mvn` 커맨드가 존재하는 폴더를 `PATH` 환경 변수에 추가해주어도 좋다. 먼저 설명한 '1. JDK 설치'에서 다룬 예제 '스크립트' 내용과 같은 식으로
적당히 관련 환경 변수를 설정해주는 '스크립트 파일'을 작성해 실행 후 `mvn` 커맨드를 실행하는 방식을 취할 수도 있겠다.

'macOS' 환경이라면 'Homebrew'를 통해서 메이븐을 설치할 수 있다. 'Homebrew'가 설치된 상태에서 다음과 같이 커맨드를 실행해서 메이븐을 설치할 수 있다.

```bash
brew install maven
```

터미널에 출력되는 설치 과정을 살피면 `mvn` 커맨드 실행에 사용할 'OpenJDK'같이 설치되는 것을 볼 수 있다. 설치 완료후 `JAVA_HOME` 환경 변수를 별도로
설정하지 않아도 `mvn` 커맨드가 정상적으로 실행된다. `mvn` 커맨드는 `which mvn` 명령어로 확인해 보면 `/opt/homebrew/bin/mvn`에 있는 것을 확인할
수 있다. `/opt/homebrew/bin`은 `PATH` 환경 변수에 이미 추가되어 있어서 추가적인 `PATH` 환경 변수 설정 변경이 필요없다.