# 메이븐 빌드시 '경고(WARNING)' 메시지 제거
앞서의 '별도 라이브러리 .jar 생성' 주제까지의 내용을 따라했다면, 일단 정상적으로 'Hello Java Word!'를 콘솔에 출력할 수 있지만 메이븐 빌드시에
'경고(WARNING)' 메시지가 나타난다. 이번 주제에서는 이러한 '경고(WARNING)' 메시지를 제거하는 방법을 알아보겠다.

먼저 작업한 'hello_java_world' 프로젝트의 폴더 구조는 다음과 같다.

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
                |
                +-- java
                    |
                    +-- com
                        |
                        +-- example
                            |
                            +-- HelloWorldLib.java
```

## 수정전 'pom.xml' 파일 내용 확인
총 3개의 'pom.xml' 파일이 있다. 수정을 진행하기전에 기존에 작업된 'pom.xml' 파일의 내용을 먼저 확인해 본다. 

### <hello_java_world>/pom.xml
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

### <hello_java_world>/hello_world_console_app/pom.xml
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

### <hello_java_world>/hello_world_lib/pom.xml
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

'최상위 프로젝트'의 'pom.xml' 파일이 위치한 폴더 위치에서 `mvn clean package`를 실행하면 정상적으로 빌드가 진행되기는 하지만 몇가지
'경고(WARNING)' 메시지가 출력된 것을 볼 수 있다.

```
(base) ghjang@Gils-MacBook-Air hello_java_world % mvn clean package
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Build Order:
[INFO] 
[INFO] hello_java_world                                                   [pom]
[INFO] hello_world_lib                                                    [jar]
[INFO] hello_world_console_app                                            [jar]
[INFO] 
[INFO] --------------------< com.example:hello_java_world >--------------------
[INFO] Building hello_java_world 1.0-SNAPSHOT                             [1/3]
[INFO]   from pom.xml
[INFO] --------------------------------[ pom ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ hello_java_world ---
[INFO] 
[INFO] --------------------< com.example:hello_world_lib >---------------------
[INFO] Building hello_world_lib 1.0-SNAPSHOT                              [2/3]
[INFO]   from hello_world_lib/pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ hello_world_lib ---
[INFO] Deleting /Users/ghjang/GitHub/hello_java_world/hello_world_lib/target
[INFO] 
[INFO] --- resources:3.3.1:resources (default-resources) @ hello_world_lib ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /Users/ghjang/GitHub/hello_java_world/hello_world_lib/src/main/resources
[INFO] 
[INFO] --- compiler:3.11.0:compile (default-compile) @ hello_world_lib ---
[INFO] Changes detected - recompiling the module! :source
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[INFO] Compiling 1 source file with javac [debug target 1.8] to target/classes
[WARNING] bootstrap class path not set in conjunction with -source 8
[WARNING] source value 8 is obsolete and will be removed in a future release
[WARNING] target value 8 is obsolete and will be removed in a future release
[WARNING] To suppress warnings about obsolete options, use -Xlint:-options.
[INFO] 
[INFO] --- resources:3.3.1:testResources (default-testResources) @ hello_world_lib ---
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory /Users/ghjang/GitHub/hello_java_world/hello_world_lib/src/test/resources
[INFO] 
[INFO] --- compiler:3.11.0:testCompile (default-testCompile) @ hello_world_lib ---
[INFO] No sources to compile
[INFO] 
[INFO] --- surefire:3.2.2:test (default-test) @ hello_world_lib ---
[INFO] No tests to run.
[INFO] 
[INFO] --- jar:3.3.0:jar (default-jar) @ hello_world_lib ---
[INFO] Building jar: /Users/ghjang/GitHub/hello_java_world/hello_world_lib/target/hello_world_lib-1.0-SNAPSHOT.jar
[INFO] 
[INFO] ----------------< org.example:hello_world_console_app >-----------------
[INFO] Building hello_world_console_app 1.0-SNAPSHOT                      [3/3]
[INFO]   from hello_world_console_app/pom.xml
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- clean:3.2.0:clean (default-clean) @ hello_world_console_app ---
[INFO] Deleting /Users/ghjang/GitHub/hello_java_world/hello_world_console_app/target
[INFO] 
[INFO] --- resources:3.3.1:resources (default-resources) @ hello_world_console_app ---
[INFO] skip non existing resourceDirectory /Users/ghjang/GitHub/hello_java_world/hello_world_console_app/src/main/resources
[INFO] 
[INFO] --- compiler:3.11.0:compile (default-compile) @ hello_world_console_app ---
[INFO] Changes detected - recompiling the module! :dependency
[INFO] Compiling 1 source file with javac [debug target 17] to target/classes
[WARNING] system modules path not set in conjunction with -source 17
[INFO] 
[INFO] --- resources:3.3.1:testResources (default-testResources) @ hello_world_console_app ---
[INFO] skip non existing resourceDirectory /Users/ghjang/GitHub/hello_java_world/hello_world_console_app/src/test/resources
[INFO] 
[INFO] --- compiler:3.11.0:testCompile (default-testCompile) @ hello_world_console_app ---
[INFO] No sources to compile
[INFO] 
[INFO] --- surefire:3.2.2:test (default-test) @ hello_world_console_app ---
[INFO] No tests to run.
[INFO] 
[INFO] --- dependency:3.6.1:copy-dependencies (copy-dependencies) @ hello_world_console_app ---
[INFO] Copying hello_world_lib-1.0-SNAPSHOT.jar to /Users/ghjang/GitHub/hello_java_world/hello_world_console_app/target/lib/hello_world_lib-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- jar:3.3.0:jar (default-jar) @ hello_world_console_app ---
[INFO] Building jar: /Users/ghjang/GitHub/hello_java_world/hello_world_console_app/target/hello_world_console_app-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for hello_java_world 1.0-SNAPSHOT:
[INFO] 
[INFO] hello_java_world ................................... SUCCESS [  0.073 s]
[INFO] hello_world_lib .................................... SUCCESS [  0.596 s]
[INFO] hello_world_console_app ............................ SUCCESS [  0.344 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.067 s
[INFO] Finished at: 2024-01-02T15:12:13+09:00
[INFO] ------------------------------------------------------------------------
```

경고 메시지 부분만 `grep` 명령어로 추출해보면 다음과 같다.

```
(base) ghjang@Gils-MacBook-Air hello_java_world % mvn clean package | grep WARNING
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[WARNING] File encoding has not been set, using platform encoding UTF-8, i.e. build is platform dependent!
[WARNING] bootstrap class path not set in conjunction with -source 8
[WARNING] source value 8 is obsolete and will be removed in a future release
[WARNING] target value 8 is obsolete and will be removed in a future release
[WARNING] To suppress warnings about obsolete options, use -Xlint:-options.
[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
[WARNING] system modules path not set in conjunction with -source 17
```

경고 메시지의 내용과 작성된 'pom.xml' 파일들의 내용을 살펴보면, 'hello_world_lib' 프로젝트의 'pom.xml' 파일에는 사용할 '자바 버전'과 자바 소스
파일의 '인코딩'을 설정하는 부분이 없다는 것을 알 수 있다.

## 'pom.xml' 파일 수정 {id="pom-xml_1"}
관련 경고 메시지 제거를 위해서 다음과 같이 2개의 'pom.xml' 파일에 내용을 수정해줄 수 있겠다. '하위 모듈 프로젝트'에서 공통으로 사용할 설정을
'상위 프로젝트'의 'pom.xml' 파일에 정의했다. 일종의 '공통 설정 리팩터링'을 진행한 것이다.

### 수정된 <hello_java_world>/pom.xml {id="hello-java-world-pom-xml_1"}
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

  <properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <modules>
    <module>hello_world_console_app</module>
    <module>hello_world_lib</module>
  </modules>
</project>
```

기존에 없던 `properties` 요소를 추가하고 하위에 `maven.compiler.source`, `maven.compiler.target`, `project.build.sourceEncoding`
 요소들을 설정했다.

### 수정된 <hello_java_world>/hello_world_console_app/pom.xml {id="hello-world-console-app-pom-xml_1"}

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

기존의 `properties` 요소를 삭제했다. 삭제된 `properties` 요소의 내용은 '상위 프로젝트'의 'pom.xml' 파일로 이동했다.

여기까지 수정을 하고 다시 `mvn clean package` 명령어를 실행해도 여전히 다음의 경고 메시지가 나타나는 것을 확인할 수 있다.

```
[WARNING] system modules path not set in conjunction with -source 17
```

이 경고 메시지는 '자바 17' 버전 이상을 사용할 경우에 나타난다. 간단하게는 `maven-compiler-plugin` 플러그인의 `source`와 `target` 요소가 아닌
`release` 요소를 사용해 문제를 해결할 수 있다. `build` 요소 하위에 `plugins` 요소를 추가하고 `maven-compiler-plugin` 플러그인을 설정을 추가후
`release` 요소를 설정할 수도 있지만 여기서는 현재 정의된 `properties` 요소의 내용을 수정해서 문제를 해결해보겠다. '<hello_java_world>/pom.xml'
파일내의 `properties` 요소에서 기존의 `maven.compiler.source`와 `maven.compiler.target` 요소를 삭제하고 `maven.compiler.release`
요소를 추가한다.

```
  <properties>
    <maven.compiler.release>17</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
```

이제 다시 `mvn clean package` 명령어를 실행하면 경고 메시지가 모두 사라진 것을 확인할 수 있다.

참고로 `properties` 요소에 정의된 `maven.compiler.release` 요소값 설정은 결과적으로 `maven-complier-plugin` 플러그인의 `source`와
`target` 요소를 설정하는 것이된다. `project.build.sourceEncoding` 요소는 특정 플러그인에 소속된 것은 아니고 '프로젝트 전체'에 적용되는 설정이다.
결과적으로 여러 플러그인에서 사용하는 '기본 인코딩'을 설정하는 것이된다.
