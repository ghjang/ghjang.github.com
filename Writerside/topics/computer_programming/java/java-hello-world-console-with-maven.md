# Maven 빌드 설정 구성
'Hello Java World!' 콘솔 프로그램을 'Maven(메이븐) 빌드 툴'로 빌드하기 위해서는 '메이븐 프로젝트 폴더 구조'를 따라서 소스 코드를 배치해야 한다.

## 표준 프로젝트 폴더 구조
메이븐 빌드 툴에서 암묵적으로 가정하는 '표준 프로젝트 폴더 구조'는 대략 다음과 같다.

```
<project root folder>
    |
    +-- pom.xml
    |
    +-- src
        |
        +-- main
        |   |
        |   +-- java
        |   |   |
        |   |   +-- <package name>
        |   |       |
        |   |       +-- <source files>
        |   |
        |   +-- resources
        |       |
        |       +-- <resource files>
        |
        +-- test
            |
            +-- java
            |   |
            |   +-- <package name>
            |       |
            |       +-- <test source files>
            |
            +-- resources
                |
                +-- <test resource files>
```

'프로젝트 루트 폴더'내에 포함된 'pom.xml' 파일에 해당 프로젝트를 빌드하기 위한 세부 설정들을 기술해야한다.

'src' 폴더 하위의 구조는 메이븐 또는 'Gradle' 빌드 툴에서 암묵적으로 가정하는 구조이다. 'Convention over Configuration'라는 개념이다.
즉, 메이븐 빌드 툴에서 사용하는 이 암묵적으로 가정하는 프로젝트 폴더 구조를 따르면 별도의 설정 없이도 쉽게 빌드를 수행할 수 있다. 이 '표준 프로젝트 폴더
구조'에 대한 보다 자세한 설명은 메이븐 공식 웹사이트의
[Introduction to the Standard Directory Layout](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)
페이지를 참고하면 된다.

참고로 이러한 관례적인 폴더 구조를 따르지 않는 (오래된) 프로젝트 소스를 메이븐으로 빌드하기 위해서는 'pom.xml'에서 `build` 요소 하위에 상황에 맞게
'소스 폴더', '리소스 폴더'등의 위치 설정을 변경 해주어야한다. 예를들면 다음과 같은 식으로 변경할 수 있다.

```xml
<build>
    <!--
        'pom.xml'내에서 '상대 경로'로 지정된 폴더 경로는
        '프로젝트 루트 폴더(== pom.xml 파일이 위치한 폴더)'를 기준으로 한다.
        기본적으로 지정된 폴더 하위의 모든 파일들이 대상이 된다.
        
        'source' 부류 폴더 하위에는 컴파일 대상인 자바 소스 파일이 위치한다.
        컴파일되어 생성된 '.class' 파일들이 '패키지' 폴더 구조를 유지하며
        '<베이스 출력 폴더(기본값 : target)>' 하위의 'classes' 또는
        'test-classes' 폴더내에 위치하게된다.
        
        'resource' 부류 폴더 하위에는 컴파일 대상이 아닌 리소스 파일들이 위치한다.
        컴파일되지 않고 그대로 '<베이스 출력 폴더(기본값 : target)>' 하위의
        'classes' 또는 'test-classes' 폴더내에 복사된다.
        리소스 파일의 예로는 '프로퍼티 파일', 'XML 파일', '이미지 파일'등이 있다.
    -->
    
    <!-- 컴파일 대상인 자바 소스 파일이 위치한 '베이스 폴더 경로'를 설정한다. -->
    <sourceDirectory>src</sourceDirectory>
    
    <resources>
        <resource>
            <directory>resources</directory>
            
            <!--
                '리소스 파일'들이 '출력 폴더'에 위치할 '하위 폴더 경로'를 설정한다.
                '기본값'은 '<출력 폴더 베이스>/classes'로 원래의 리소스 폴더 경로를
                유지하지 않고 단순히 '클래스 경로' 하위에 위치시켜 자바 소스 코드에서
                '클래스 경로'를 기준으로 리소스 파일을 찾을 수 있게 해준다.
                
                리소스 파일의 원래 폴더 구조를 유지하고 싶다면 다음과 같이 설정할 수 있다.
                `${project.build.outputDirectory}`는 'build > directory'
                요소에 지정된 '출력 폴더 베이스' 경로를 가리키는 메이븐 프로젝트 변수이다.
            -->
            <targetPath>${project.build.outputDirectory}/classes/resources</targetPath>
        </resource>
    </resources>
    
    <!-- '단위 테스트'등을 위한 자바 소스 파일이 위치한 '베이스 폴더 경로'를 설정한다. -->
    <testSourceDirectory>test_src</testSourceDirectory>
    
    <testResources>
        <testResource>
            <directory>test_resources</directory>
        </testResource>
    </testResources>
    
    <!--
        컴파일된 클래스 파일과 리소스 파일이 위치할 '출력 베이스 폴더 경로'를 설정한다.
        별도로 지정하지 않으면 'target'이 '기본값'으로 사용된다.
    -->
    <directory>out</directory>
</build>
```

## 'Hello Java World!' 콘솔 프로그램 빌드용 'pom.xml' 파일 작성

메이븐의 '표준 프로젝트 폴더 구조'를 그대로 사용해 간단하게 'Hello Java World!' 자바 콘솔 프로그램을 빌드하는 방법에 대해서 설명한다.

최소한의 'pom.xml' 파일에 포함될 내용은 대략 다음과 같다. '인텔리제이' IDE를 사용해서 자바 콘솔 프로젝트 생성시에 '메이븐'을 빌드 툴로 선택했을때
생성되는 내용을 붙여넣은 것이다.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>hello_java_world</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

</project>
```

### 'project' 요소

`project` 요소는 메이븐 프로젝트의 최상위 요소이다. 이 요소의 '속성' 설정은 제공된 템플릿의 것을 수정없이 그대로 사용하면 된다.

### 'modelVersion' 요소

`modelVersion` 요소는 '메이븐 모델'의 버전을 지정한다. 역시 이 요소의 '속성' 설정은 제공된 템플릿의 것을 수정없이 그대로 사용해도 별다른 문제가 없겠다.

### 'groupId, artifactId, version' 요소

이 요소들은 메이븐으로 빌드하려는 프로젝트의 '고유 식별자'를 구성할 수 있게 해준다.

`groupId` 요소에는 마치 '자바 패키지 명'처럼 보이는 문자열값이 지정된 것을 자주 보게된다. 프로젝트를 진행하는 '그룹, 조직' 구분 정보를 입력하는 것인데,
보통 회사 또는 조직의 '도메인 이름'을 거꾸로 적어서 사용한다. '자바 패키지 명'과는 별도의 개념이다. 하지만 `groupId`에 '주 상위 자바 패키지 명' 접두사를
사용하는 경우가 많다.

`artifactId` 요소에는 `groupId`에 지정된 '그룹, 조직'이 진행하는 프로젝트의 '프로젝트 명'을 입력한다. 역시 '자바 패키지 명'과는 별도의 개념이다.

`version` 요소에는 대상 프로젝트의 '버전'을 입력한다. '버전'은 보통 '메이저 버전', '마이너 버전', '패치 버전'으로 구성된다. 하지만 꼭 그런 규칙을
따라야 하는 것은 아니다. 앞서의 예제 코드에서는 '1.0-SNAPSHOT'이라는 버전 형식을 사용했다. 'SNAPSHOT'이라는 접미사는 '개발 버전'을 의미한다. 구분만
될 수 있다면 어떤 버전 문자열이라도 사용할 수 있다. 예를 들어서 '1.0.0', '1.0.0-SNAPSHOT', '1.0.0-RELEASE'등의 버전 형식을 사용할 수도 있다.

'CI' 시스템을 사용해서 '자동빌드'를 구성하는 경우 '버전'을 자동으로 생성해 관리하는게 보통이다. 'CI' 시스템에서 어떤 스크립트를 통해서 빌드 진행시 해당
'pom.xml' 파일의 `version` 요소값을 직접 갱신해주거나, 메이븐의 '환경변수' 참조 문법을 사용해 간접적으로 'CI'쪽에서 정의해준 버전 관련 '환경변수'
정보로부터 버전값을 구성할 수도 있다. 

`mvn package` 명령어를 사용해서 빌드를 수행하면 '출력 베이스 폴더(기본값 :  target)'에 'jar' 파일이 생성된다. 이 'jar' 파일의 이름은
`artifactId`에 지정된 값과 `version`에 지정된 값으로 구성된다. 예제 코드에서 보이는 것과 같이 `artifactId`가 `hello_java_world`이고
`version`이 `1.0-SNAPSHOT`인 경우에 'hello_java_world-1.0-SNAPSHOT.jar' 파일이 생성된다. `{artifactId}-{version}.{packaging}`의
형식으로 출력 파일의 이름이 구성된 것이다. `packaging`은 기본적으로는 'jar'로 지정된다. `packaging`에는 'jar', 'war' 등의 값이 올 수
있다. 'jar' 파일은 '자바 라이브러리(java archive)'를 의미한다. 'war' 파일은 '웹 애플리케이션(web archive)'을 의미한다.

`mvn install` 명령어를 실행해 생성된 패키지 파일을 '로컬 레포지토리'에 설치하면 '로컬 레포지토리'에는 다음과 같은 경로에 'jar' 파일이 저장된다.

```
<로컬 레포지토리 경로>/org/example/hello_java_world/1.0-SNAPSHOT/hello_java_world-1.0-SNAPSHOT.jar
```

'macOS'의 경우라면 별다른 '메이븐 설정(settings.xml)'을 변경하지 않았을때 '<로컬 레포지토리 경로>'는 '홈 디렉토리'이다. '로컬 페포지토리
경로(`localRepository`)'를 원하는 베이스 경로로 바꾸고 싶다면 'settings.xml' 파일을 수정하면 된다. 'settings.xml' 파일은 메이븐 설치 폴더의
'conf' 폴더에 위치한다.

```
~/.m2/repository/org/example/hello_java_world/1.0-SNAPSHOT/hello_java_world-1.0-SNAPSHOT.jar
```

### 'properties' 요소

`properties` 요소의 내용은 '필수'적인 것은 아니다. 하지만 추후 유지보수를 위해서 명시적으로 사용할 자바 버전을 지정해주는 것이 좋다.
'source'와 'target'에 대해서 서로 다른 버전을 지정하는 것이 가능하다. 이것의 의미는 지정된 버전의 JDK 규격으로 작성된 소스 코드를 'target'에 지정된
버전으로 (크로스) 컴파일한다는 의미이다. 예를 들어서 높은 버전의 JDK를 이용해서 낮은 버전의 JVM을 타겟팅하는 '.class' 파일 생성이 가능하다. 물론 소스
코드에는 타겟 JVM에서 지원하지 않는 기능을 사용하지 않아야 한다. 참고로 생성되는 '.class' 파일 내부에 어떤 버전의 JDK 규격으로 타겟해서 컴파일된 것인지에
대한 버전 정보가 포함되어 있다. 그리고 'UTF-8'로 인코딩된 소스 코드를 사용한다면 `project.build.sourceEncoding` 요소를 지정해주는 것이 맞겠다.

`properties` 요소 하위에는 '커스텀 속성'을 추가적으로 정의할 수도 있다. 예를 들어서 다음과 같이 '프로젝트 버전'을 '커스텀 속성'으로 정의할 수 있다.

```xml
<properties>
    <project.version>1.0.0</project.version>
</properties>
```

이렇게 정의된 '커스텀 속성'은 '프로젝트 루트 폴더'내의 'pom.xml' 파일에서 다음과 같이 참조할 수 있다.

```xml
<version>${project.version}</version>
```

당연히 어떤 설정값의 부분 표현으로 사용할 수도 있다. 예를 들어서 다음과 같이 '프로젝트 버전'을 '커스텀 속성'으로 정의하고 그 값을 다른 설정값의 부분 표현으로
사용할 수 있다.

```xml
<file.name>xxx-${project.version}.yyy</file.name>
```

'pom.xml' 파일에서 특정 표현값을 반복해서 참조할 경우 그 값을 커스텀 속성으로 정의해서 좀더 간결하게 중복을 제거해 설정을 표현할 수 있다. (자바 소스 코드
리팩터링의 관점에서 생각해보면 일종의 '변수 추출'이라고 볼 수 있다.)
