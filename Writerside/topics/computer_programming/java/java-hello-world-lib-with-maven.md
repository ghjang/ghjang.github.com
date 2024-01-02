# 별도 라이브러리 '.jar' 생성

'메이븐'은 '멀티 모듈 프로젝트'를 지원한다. '멀티 모듈 프로젝트'란 여러 개의 '하위 프로젝트'를 하나의 '상위 프로젝트'로 묶어서 관리하는 것을 말한다.
'멀티 모듈 프로젝트'를 사용하면 여러 개의 하위 프로젝트를 하나의 상위 프로젝트로 묶어서 관리할 수 있기 때문에 프로젝트 간의 의존성을 관리하기가 쉽다. 또한,
프로젝트를 묶어서 관리하기 때문에 프로젝트 간의 '공통 설정'을 관리하기가 쉽다.

여기서는 'Hello Java World!'를 '콘솔'에 출력하는 자바 콘솔 프로젝트의 일부 내용을 라이브러리 성격의 별도 프로젝트로 분리하고, 콘솔 프로젝트에서
새로 작성된 라이브러리 프로젝트를 참조하여 'Hello Java World!'를 콘솔에 출력하도록 변경해본다. 대략적인 멀티 모듈 프로젝트 작업 방식을 이해해보기 위한
목적이다.

참고로 하위 '모듈(module) 프로젝트'로 지정될 수 있는 것은 '메이븐 빌드' 형식을 따르는 어떠한 프로젝트여도 상관이 없겠다. 여기서는 1개의 '자바 콘솔
프로젝트'와 1개의 '라이브러리 프로젝트'를 생성해서 '멀티 모듈 프로젝트'를 구성해본다.

## 1. '상위 프로젝트' 초기 생성

`mvn` 커맨드는 사전에 정의된 '프로젝트 템플릿'을 이용해서 새로운 프로젝트의 기본 구조를 생성해주는 옵션을 제공한다. 다음과 같은 명령어를 사용해
`quickstart` 성격의 프로젝트 템플릿을 이용해서 기본 구조를 생성할 수 있다.

```Bash
mvn archetype:generate -DgroupId=com.example -DartifactId=hello_java_world -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

앞으로 언급할 '최상위 프로젝트'라는 개념은 'Visual Studio' IDE 환경으로 비유하면 '하위 프로젝트'를 묶어 관리할 수 있는 '최상위 빈 솔루션(.sln)'으로
생각할 수 있다. 사용한 커맨드에서 `artifactId`를 'hello_java_world'로 지정해 최상위 프로젝트명을 'hello_java_world'로 지정했다. 이 명령어를
'터미널' 앱에서 실행하면 'hello_java_world' 디렉토리가 생성되고 하위에 다음과 같은 기본 구조의 폴더와 파일이 생성된다.

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

생성한 프로젝트는 '하위 모듈 프로젝트'를 묶어서 관리할 용도의 프로젝트로 'pom.xml' 파일을 제외한 '소스 코드'등은 불필요하다. 'src' 디렉토리를
삭제해준다. 앞서의 `mvn` 명령어로 생성한 'pom.xml' 파일의 내용은 대략 다음과 같다.

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

'하위 프로젝트 관리' 용도의 '최상위 프로젝트'에서 불필요한 설정 내용을 제거하고 '멀티 모듈 프로젝트'를 관리하기 위한 것과 관련된 내용만을 남긴다. 수정한
'최상위 프로젝트'의 'pom.xml' 파일의 초기 내용은 다음과 같다.

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
프로젝트'는 'target' 폴더를 생성하지 않는다. 아직 '하위 모듈 프로젝트'가 없기 때문에 `modules` 요소는 일단 추가하지 않았다.

## 2. '하위 모듈 프로젝트' 생성

### 'hello_world_console_app' 프로젝트 생성

'Hello Java World!' 자바 콘솔 프로젝트의 내용이 거의 없기 때문에 `mvn` 명령어로 새로 하위 모듈 프로젝트를 생성해도 되지만, 여기서는 기존에 작업한
'Hello Java World!' 자바 콘솔 프로젝트를 하위 모듈 프로젝트에 추가하도록 한다. 기존의 프로젝트 폴더를 '최상위 프로젝트' 폴더 아래
'hello_world_console_app' 폴더로 이동시킨다. 그러면 현재까지의 폴더 구조는 다음과 같을 것이다.

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

'hello_world_lib' 프로젝트는 'hello_world_console_app' 프로젝트에서 참조할 라이브러리 성격의 '.jar' 파일을 생성하는 프로젝트로 구성할 것이다.
초기 'hello_world_lib' 프로젝트를 생성하기 위해 '최상위 프로젝트' 폴더 위치에서 다음과 같은 명령어를 실행한다.

```Bash
mvn archetype:generate -DgroupId=com.example -DartifactId=hello_world_lib -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

그러면 현재까지 폴더 구조는 다음과 같게 될 것이다.

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

`modules` 요소가 자동으로 추가되었고, `modules` 요소의 하위 요소 `module`에 'hello_world_lib' 프로젝트 참조명(폴더명)이 추가되었다. 이제
`modules` 요소에 추가로 이전에 수동으로 이동시킨 'hello_world_console_app' 프로젝트를 하위 모듈 프로젝트로 추가하기 위해 '상위 프로젝트'의
'pom.xml' 파일을 다음과 같이 마저 수정해준다.

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

빌드가 성공적이었다면 콘솔에 대략 다음과 유사한 메시지가 출력되었을 것이다.

```
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for hello_java_world 1.0-SNAPSHOT:
[INFO] 
[INFO] hello_java_world ................................... SUCCESS [  0.001 s]
[INFO] hello_world_lib .................................... SUCCESS [  0.659 s]
[INFO] hello_world_console_app ............................ SUCCESS [  0.208 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.918 s
[INFO] Finished at: 2023-12-31T21:04:21+09:00
[INFO] ------------------------------------------------------------------------
``` 

## 4. 'hello_world_lib' 프로젝트 수정

### 'pom.xml' 파일 수정

아래는 생성된 'hello_world_lib' 프로젝트의 'pom.xml' 파일을 연 후에 불필요한 부분을 제거하고 리포맷팅한 것이다.

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
</project>
```

상위 프로젝트인 'hello_java_world' 프로젝트를 상속받도록 `parent` 요소가 추가된 것을 확인할 수 있다. 이런 방식으로 상위 프로젝트와의 연결 고리가
형성된 것이다. `parent` 요소 하위에 추가된 정보를 이용해서 상위 프로젝트의 'pom.xml' 파일을 참조할 수 있게 되는 것이다. '프로젝트 상속'을 받는다는
것의 의미는 상위 프로젝트의 설정을 상속받는다는 의미이다. 중복되는 설정을 상위 프로젝트에서 관리하고 하위 프로젝트에서는 상위 프로젝트의 설정을 상속받아
사용하도록 한다. 이렇게 하면 하위 프로젝트에서는 중복되는 설정을 관리할 필요가 없어진다. 예를 들어서 상위 프로젝트에 정의된 '커스텀 속성 요소'의 값을 하위
프로젝트에서 사용하거나 `dependency` 요소를 상위 프로젝트에서 관리하고 하위 프로젝트에서는 상위 프로젝트의 `dependency` 요소를 상속받아 사용할 수 있다.

`packaging` 요소는 별도로 지정하지 않았을 경우 기본값으로 `jar`가 지정된다. 따라서 'hello_world_lib' 프로젝트를 빌드하면 결과로
'hello_world_lib-1.0-SNAPSHOT.jar'이 생성된다. 

### 라이브러리 코드 작성

'단위 테스트' 코드는 별도의 주제글로 분리해서 설명한다. 여기서는 '콘솔 프로그램'에서 참조할 '라이브러리'에 해당하는 코드만 작성하겠다. 해서
'AppTest.java' 파일은 삭제하고 진행한다. 'App.java' 파일의 이름을 좀더 의미 있도록 'HelloWorldLib.java'로 변경한다. 그리고 다음과 같이
'HelloWorldLib.java' 파일의 내용을 작성해준다.

```Java
package com.example;

public class HelloWorldLib {
    public static void sayHello(String name) {
        System.out.println("Hello " + name + " World!");
    }
}
```

## 5. 'hello_world_console_app' 프로젝트 수정

### 'pom.xml' 파일 수정 {id="pom-xml_1"}

수동으로 이동시켜 생성한 'hello_world_console_app' 하위 모듈 프로젝트의 'pom.xml' 파일에 `parent` 요소를 추가해 상위 프로젝트의 설정을 상속받도록
한다. 'hello_world_lib' 프로젝트를 참조하도록 `dependency` 요소를 추가한다. `dependency` 요소를 이용한 '프로젝트간 의존성' 설정은 이후에
`mvn` 커맨드를 이용해 (최상위) 프로젝트 빌드시에 '빌드 순서(build order)'를 계산하는데 사용되기도 한다. 'hello_world_console_app' 프로젝트
자체의 `artifactId` 요소의 값을 기존 'console_app'에서 'hello_world_console_app'으로 변경했다.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.example</groupId>
        <artifactId>hello_java_world</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    
    <groupId>org.example</groupId>
    <artifactId>hello_world_console_app</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.example</groupId>
            <artifactId>hello_world_lib</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- '의존 jar 파일'을 '<출력 폴더>/lib' 하위에 복사 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>copy-dependencies</id>
                        <phase>prepare-package</phase>
                        <goals>
                            <goal>copy-dependencies</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>${project.build.directory}/lib</outputDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

            <!--
                '의존 jar 파일'이 존재하는 '<출력 폴더>/lib' 폴더 경로를
                'MANIFEST.MF' 파일의 'Class-Path' 속성 값으로 추가
            -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <classpathPrefix>lib/</classpathPrefix>
                            <mainClass>HelloWorld</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

`plugins` 요소 하위에 `maven-dependency-plugin`이 추가되었다. 이 플러그인은 해당 프로젝트의 '의존 jar 파일'을 지정된 폴더로 복사해주는
플러그인이다.

`maven-dependency-plugin` 요소 하위 `execution` 요소에 몇가지 하위 요소가 추가되었다. `id` 요소에는 '의존 jar 파일'을 복사하는 명령어를
구분하기 위한 식별자 값이 지정되었다. `phase` 요소에는 `prepare-package` 값이 설정되었다. `prepare-package`는 '컴파일된 클래스 파일'을
'패키징'하기 전에 실행되는 단계를 의미한다.`goals` 요소 하위의 `goad` 요소에 `copy-dependencies` 값이 지정되었다. `copy-dependencies`는
'의존 jar 파일'을 지정된 위치로 복사하는 목적으로 사용되었다. `configuration` 요소 하위에 `outputDirectory` 요소가 추가되었고
`outputDirectory` 요소의 값으로 '의존 jar 파일'을 복사할 폴더 경로가 지정되었다. 예제에서는 프로젝트 빌드시 '의존 jar 파일'을 'target/lib'
폴더로 복사하게 된다.

`plugins` 요소 하위에 기존에 있던 `maven-jar-plugin`의 설정 내용이 추가되었다. 추가된 내용은 'jar 파일 패키징'시에
`maven-dependency-plugin`을 이용해서 복사해준 '의존 jar 파일'이 존재하는 폴더 경로를 'MANIFEST.MF' 파일의 'Class-Path' 속성 값으로 추가하는
설정이다. '의존 jar 파일'이 존재하는 폴더 경로는 `addClasspath`와 `classpathPrefix` 요소 값으로 지정한다. 예제에서는 '의존 jar 파일'이
'target/lib' 폴더에 복사되기 때문에 `classpathPrefix` 요소의 값으로 'lib/'이 지정되었다.

### 'HelloWorld.java' 파일 수정

'HelloWorld.java' 파일의 내용을 다음과 같이 수정한다.

```Java
import com.example.HelloWorldLib;

public class HelloWorld {
    public static void main(String[] args) {
        HelloWorldLib.sayHello("Java");
    }
}
```

## 6. '최상위 프로젝트' 빌드 및 실행

이제 '최상위 프로젝트'를 빌드해보자. '최상위 프로젝트'의 'pom.xml' 파일이 위치한 곳에서 다음과 같은 명령어를 실행한다.

```Bash
mvn package
```

정상적으로 빌드가 완료되면 'hello_world_console_app' 프로젝트의 'target' 폴더에 'hello_world_console_app-1.0-SNAPSHOT.jar' 파일이
생성된다. 'target/lib' 폴더에는 'hello_world_lib-1.0-SNAPSHOT.jar' 파일이 복사된 것을 확인할 수 있다. 터미널 앱에서 해당 'target' 폴더로
이동한 후에 다음과 같은 명령어를 실행하면 'hello_world_console_app-1.0-SNAPSHOT.jar' 파일이 실행되면서 'Hello Java World!'가 콘솔에
출력되는 것을 확인할 수 있을 것이다.

```Bash
java -jar hello_world_console_app-1.0-SNAPSHOT.jar
```

변경된 'MANIFEST.MF' 파일의 내용을 확인위해서 다음과 같이 `unzip` 명령어를 실행한다.

```Bash
unzip -p hello_world_console_app-1.0-SNAPSHOT.jar META-INF/MANIFEST.MF
```

콘솔에 출력된 'MANIFEST.MF' 파일의 내용에 'Class-Path' 속성 값으로 '의존 라이브러리 프로젝트'에서 패키징된후 'target/lib' 폴더에 복사된
'lib/hello_world_lib-1.0-SNAPSHOT.jar' 파일이 추가된 것을 확인할 수 있다. 이렇게 'Class-Path' 속성 값이 설정되었기 때문에 별도의 '자바
클래스 경로' 옵션 지정없이 `java -jar` 명령어 옵션만으로 'hello_world_console_app-1.0-SNAPSHOT.jar' 파일을 실행할 수 있는 것이다.

```Text
Manifest-Version: 1.0
Created-By: Maven JAR Plugin 3.3.0
Build-Jdk-Spec: 21
Class-Path: lib/hello_world_lib-1.0-SNAPSHOT.jar
Main-Class: HelloWorld
```
