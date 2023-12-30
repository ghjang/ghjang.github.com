# 별도 라이브러리 '.jar' 생성

'메이븐'은 '멀티 모듈 프로젝트'를 지원한다. '멀티 모듈 프로젝트'란 여러 개의 프로젝트를 하나의 '상위 프로젝트'로 묶어서 관리하는 것을 말한다. '멀티 모듈
프로젝트'를 사용하면 여러 개의 하위 프로젝트를 하나의 상위 프로젝트로 묶어서 관리할 수 있기 때문에 프로젝트 간의 의존성을 관리하기가 쉽다. 또한, 프로젝트를
묶어서 관리하기 때문에 프로젝트 간의 공통 설정을 관리하기가 쉽다.

여기서는 'Hello Java World!'를 출력하는 'HelloWorld' 프로젝트를 'Hello World Lib' 프로젝트로 분리하고, 'HelloWorld' 프로젝트에서
'Hello World Lib' 프로젝트를 참조하여 'Hello Java World!'를 출력하는 프로그램을 작성해 본다. 대략적인 작업 방식을 이해하면 되겠다.

## 1. '상위 프로젝트' 초기 생성

```Bash
mvn archetype:generate -DgroupId=com.example -DartifactId=hello_java_world -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

'Visual Studio' IDE 환경으로 비유하면 '최상위 빈 솔루션(.sln)'을 생성하는 것으로 생각할 수 있다. 여기서는 `artifactId`를
'hello_java_world'로 지정해 최상위 프로젝트명을 'hello_java_world'로 지정한 것이다. 이 명령어를 '터미널' 앱에서 실행하면 'hello_java_world'
디렉토리가 생성된다. 다음과 같은 폴더 구조가 생성된다.

```Text
<hello_java_world>
    |
    +-- pom.xml
    |
    +-- src
        +-- main
        |   |
        |   +-- java
        |       |
        |       +-- com
        |           |
        |           +-- example
        |               |
        |               +-- App.java
        +-- test
            |
            +-- java
                |
                +-- com
                    |
                    +-- example
                        |
                        +-- AppTest.java
```

여기서 'pom.xml' 파일을 제외하고는 불필요하다. 'src' 디렉토리를 삭제한다. 앞서의 `mvn` 명령어로 생성한 'pom.xml' 파일의 내용은 대략 다음과 같다.

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>hello-java-world</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>hello-java-world</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

'최상위 프로젝트'에서 불필요한 내용을 제거하고 '멀티 모듈 프로젝트'를 생성하기 위한 내용을 추가한다. 수정한 '최상위 프로젝트'의 'pom.xml' 파일의 초기
내용은 다음과 같다.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>hello_java_world</artifactId>
  <version>1.0-SNAPSHOT</version>

  <packaging>pom</packaging>
</project>
```

적당히 'pom.xml' 파일을 리포맷팅 및 수정했고, `packaging` 요소의 값을 `jar`에서 `pom`으로 변경했다. `packaging`을 `pom`으로 지정하면 '최상위
프로젝트'는 'target' 폴더를 생성하지 않는다. 아직 '하위 모듈 프로젝트'가 없기 때문에 `modules` 요소를 추가되지 않았다.

## 2. '하위 모듈 프로젝트' 생성

### 'hello_world_console_app' 프로젝트 생성

'Hello Java World!' 자바 콘솔 프로젝트의 내용이 거의 없기 때문에 `mvn` 명령어로 새로 하위 모듈 프로젝트를 생성해도 되지만, 여기서는 기존에 작업한
'Hello Java World!' 자바 콘솔 프로젝트를 하위 모듈 프로젝트에 추가하도록 한다. 기존의 프로젝트 폴더를 '최상위 프로젝트' 폴더 아래
'hello_world_console_app' 폴더로 이동시킨다. 그러면 현재까지의 폴더 구조는 다음과 같다.

```Text
<hello_java_world>
    |
    +-- pom.xml
    |
    +-- hello_world_console_app
        |
        +-- pom.xml
        |
        +-- src
            +-- main
                |
                +-- java
                    |
                    +-- HelloWorld.java
```

### 'hello_world_lib' 프로젝트 생성

'hello_world_lib' 프로젝트는 'hello_world_console_app' 프로젝트에서 참조할 라이브러리를 생성하는 프로젝트로 구성할 것이다. 초기
'hello_world_lib' 프로젝트를 생성하기 위해 '최상위 프로젝트' 폴더 위치에서 다음과 같은 명령어를 실행한다.

```Bash
mvn archetype:generate -DgroupId=com.example -DartifactId=hello_world_lib -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

그러면 현재까지 폴더 구조는 다음과 같다.

```Text
<hello_java_world>
    |
    +-- pom.xml
    |
    +-- hello_world_console_app
    |   |
    |   +-- pom.xml
    |   |
    |   +-- src
    |       +-- main
    |           |
    |           +-- java
    |               |
    |               +-- HelloWorld.java
    |
    +-- hello_world_lib
        |
        +-- pom.xml
        |
        +-- src
            +-- main
            |   |
            |   +-- java
            |       |
            |       +-- com
            |           |
            |           +-- example
            |               |
            |               +-- App.java
            +-- test
                |
                +-- java
                    |
                    +-- com
                        |
                        +-- example
                            |
                            +-- AppTest.java
```

## 3. '상위 프로젝트' 설정 수정

여기서 설명한 방식을 그대로 따라 직전 단계에서의 `mvn` 명령어로 'hello_world_lib' 하위 프로젝트까지 생성했다면 '상위 프로젝트'의 'pom.xml' 파일이
자동으로 다음과 같이 갱신된 것을 확인할 수 있다. 포맷팅이 바뀌어 있게되는데 편의상 리포맷팅 했다.

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>hello_java_world</artifactId>
  <version>1.0-SNAPSHOT</version>

  <packaging>pom</packaging>

  <modules>
    <module>hello_world_lib</module>
  </modules>

</project>
```

`modules` 요소가 자동으로 추가되었고, `modules` 요소의 하위 요소로 'hello_world_lib' 프로젝트가 추가되었다. 이제 `modules` 요소에 추가로
이전에 수동으로 이동시킨 'hello_world_console_app' 프로젝트를 하위 모듈 프로젝트로 추가하기 위해 '상위 프로젝트'의 'pom.xml' 파일을 다음과 같이
수정한다.

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>hello_java_world</artifactId>
  <version>1.0-SNAPSHOT</version>

  <packaging>pom</packaging>

  <modules>
    <module>hello_world_console_app</module>
    <module>hello_world_lib</module>
  </modules>

</project>
```

아직 '라이브러리' 성격의 'hello_world_lib' 프로젝트의 내용을 구성하지는 않았지만 이제 '최상위 프로젝트'를 `mvn` 명령어로 빌드할 수 있는 상태가 되었다.
최상위 프로젝트의 'pom.xml' 파일이 위치한 곳에서 다음과 같은 명령어를 실행하면 일단 정상적으로 '최상위 프로젝트 1개'와 '하위 모듈 프로젝트 2개'가
정상적으로 빌드 진행되는 것을 확인할 수 있겠다.

```Bash
mvn package
```
