# 단위 테스트 추가

'JUnit'을 사용해 '단위 테스트' 코드를 추가할 수 있다. 메이븐을 통해서 JUnit 의존성을 추가하고 단위 테스트를 싫행하는 방법을 알아보자.

## 1. JUnit 의존성 추가
작성된 'hello_world_lib' 하위 모듈 프로젝트에 단위 테스트를 추가해보자. 단위 테스트를 추가하기 위해서는 'JUnit' 의존성을 추가해야 한다.
'hello_world_lib' 하위 모듈 프로젝트의 'pom.xml' 파일에 'JUnit' 의존성을 우선 추가해준다.

```
<?xml version="1.0"?>
<project
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd"
  xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <modelVersion>4.0.0</modelVersion>

  <parent>
    <groupId>com.example</groupId>
    <artifactId>hello_java_world</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>

  <groupId>com.example</groupId>
  <artifactId>hello_world_lib</artifactId>
  <version>1.0-SNAPSHOT</version>

  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.2</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

`groupId`가 'junit'인 의존성이 추가되었다. 해당 의존성의 `scope`가 'test'로 설정되어 있기 때문에, 이 의존성은 테스트 관련 코드에서만 유효하다.
'hello_world_lib' 프로젝트를 의존성으로 사용하는 또다른 하위 모듈 프로젝트 'hello_world_console_app'에서는 해당 의존성이 노출되지 않는다. 따라서
'hello_world_console_app' 프로젝트에서는 'JUnit'을 곧바로 사용할 수는 없다. 'hello_world_console_app' 프로젝트 자체에도 단위 테스트 코드를
작성해야할 필요가 있고 'JUnit'으로 테스트를 수행하길 원하면 같은 방식으로 'JUnit' 의존성을 추가해주고 작업하면 된다. 모든 하위 모듈 프로젝트에서 각각
단위 테스트를 실행할 필요가 있다면 'JUnit' 의존성 설정을 '상위 프로젝트'의 'pom.xml' 파일에 '공통 의존성'으로 1번만 설정해 주는 것이 더 나은 방법일
수도 있다.

'hello_world_console_app' 프로젝트에는 'maven-dependency-plugin' 플러그인 설정을 추가하고 의존성 모듈 파일을 빌드 결과 출력 폴더 하위의
'lib' 폴더로 복사하게 했었다. 'hello_world_lib' 프로젝트에 설정된 'JUnit' 의존성은 'hello_world_console_app' 프로젝트에는 노출되지 않았고
`scope`가 'test'로 설정되어 있기 때문에 'JUnit' 관련 의존성 모듈 파일은 'hello_world_console_app' 프로젝트의 'lib' 폴더에 복사되지 않는다.

## 2. 단위 테스트 코드 작성

메이븐 빌드 툴과 JUnit을 사용해서 단위 테스트를 실행하려면 단위 테스트 코드가 메이븐에서 인식할 수 있는 위치에 있어야 한다. 메이븐은 단위 테스트 코드를
'테스트 소스 코드'라고 부르며, 별다른 설정 변경이 없을 경우 기본적으로는 'src/test/java' 폴더 하위에 위치한 단위 테스트 코드를 인식한다. 따라서
'hello_world_lib' 프로젝트의 'src/test/java' 폴더 하위에 단위 테스트 코드를 작성하면 된다. 테스트 코드 자체를 '자바 패키지' 구조에 꼭 맞출 필요는
없다. 'HelloWorldLibTest.java' 파일을 'src/test/java' 폴더 하위에 생성하고 다음과 같이 테스트 코드를 작성해준다.

```Java
import org.junit.Test;

import com.example.HelloWorldLib;

public class HelloWorldLibTest {
    @Test
    public void testHelloWorld() {
        HelloWorldLib.sayHello("Java");
    }
}
```

`testHelloWorld`라는 이름의 테스트 메쏘드 1개를 작성했다. 작성된 코드의 의미는 'HelloWorldLib' 클래스의 `sayHello` 메쏘드를 단순히 호출하는
것이다. `sayHello` 메쏘드가 '예외'와 같은 오류를 발생시키지 않는다면, 이 테스트 메쏘드는 '성공'한 것으로 간주되어 해당 테스트가 통과된 것으로 간주된다.

'HelloWorldLibTest' 테스트 클래스까지 작성하면 전체 프로젝트 구조를 다시 한번 더 확인을 위해서 적어보면 다음과 같다.

```
<hello_java_world>
    |
    +-- pom.xml
    |
    +-- hello_world_console_app
    |   |
    |   +-- pom.xml
    |   |
    |   +-- src
    |       |
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
            |
            +-- main
            |   |
            |   +-- java
            |       |            
            |       +-- com
            |           |
            |           +-- example
            |               |
            |                +-- HelloWorldLib.java
            +-- test
                |
                +-- java
                    |
                    +-- HelloWorldLibTest.java
```

## 3. 단위 테스트 실행

단위 테스트를 실행하려면 메이븐 빌드 툴을 사용하면 된다. 'hello_world_lib' 프로젝트의 'pom.xml' 파일이 있는 폴더에서 다음과 같이 명령어를 입력하면
단위 테스트를 실행할 수 있다.

```bash
mvn test
```

상위 프로젝트인 'hello_java_world' 폴더에서도 `mvn test` 명령어를 입력해서 'hello_world_lib' 프로젝트의 단위 테스트를 실행할 수도 있다. 물론
이 경우는 'hello_world_lib' 프로젝트의 단위 테스트만 실행되는 것이 아니라 모든 하위 모듈 프로젝트의 단위 테스트를 실행한다는 의미이다.
'hello_java_world' 프로젝트의 'pom.xml' 파일이 있는 폴더 위치에서 특정 하위 모듈 프로젝트의 `module`명을 지정해서 단위 테스트를 실행할 수도 있다.

```bash
mvn test -pl hello_world_lib
```

`-pl` 옵션은 '프로젝트 리스트'를 의미한다. `-pl` 옵션 대신에 `--projects` 옵션을 사용할 수도 있다. 2개 이상의 하위 모듈 프로젝트를 지정할 때는
콤마(,)로 구분해서 지정하면 된다.

```bash
mvn test -pl hello_world_lib,hello_world_console_app
```

참고로 `mvn package` 명령어를 사용하면 단위 테스트를 실행하고 빌드 결과를 출력하는 것까지 한번에 수행하게 된다. 'package'는 'test' 단계를 포함하는
개념으로 정의되었기 때문이다.

별다른 문제없이 단위 테스트가 실행되면 다음과 유사한 내용을 포함하는 결과가 출력될 것이다.

```
...
...
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
[INFO] Running HelloWorldLibTest
Hello Java World!
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.024 s -- in HelloWorldLibTest
[INFO] 
[INFO] Results:
[INFO] 
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
...
...
```

작성한 단위 테스트 메쏘드 `testHelloWorld`가 실행되었고, `HelloWorldLib.sayHello` 메쏘드가 정상적으로 실행되어 'Hello Java World!'라는
메시지가 콘솔에 출력된 것을 확인할 수 있다.
