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
