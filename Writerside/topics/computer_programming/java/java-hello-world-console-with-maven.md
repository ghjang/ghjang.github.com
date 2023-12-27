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

## 'Hello Java World!' 콘솔 프로그램 빌드용 기본 'pom.xml' 파일 작성

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

보다시피 `groupId`로 지정한 'org.example' 문자열 값에 '.'이 포함된 경우 '.' 문자를 기준으로 'org/example' 서브 폴더 구조가 생성되었다.
앞서 언급한 것처럼 `groupId`는 '자바 패키지'와 딱히 관련이 없지만 폴더 구조를 구성할때는 '자바 패키지'와 비슷한 형태로 구성되었다.

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

## 'Hello Java World!' 콘솔 프로그램 소스 코드 작성/빌드/실행

편의상 먼저 작성했던 'HelloWorld.java' 파일의 내용을 아래에 다시 붙였다.

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello Java World!");
    }
}
```

'Hello Java World!' 콘솔 프로그램 소스 코드 파일(HelloWorld.java)을 메이븐 '표준 프로젝트 폴더 구조'에 맞게 배치한다. '패키지'를 명시적으로
지정하지 않았기 때문에 곧바로 'src/main/java' 폴더에 위치시키면 된다.

```
<project root folder>
    |
    +-- pom.xml
    |
    +-- src
        |
        +-- main
            |
            +-- java
                |
                +-- HelloWorld.java
```

메이븐으로 이 '자바 콘솔 어플리케이션'을 빌드하기 위해서 '터미널' 앱을 구동한 후 'pom.xml' 파일이 위치한 '프로젝트 루트 폴더'로 이동한 후 다음과 같이
명령어를 실행한다.

```bash
mvn package
```

명령어 실행이 정상적으로 완료되면 'target' 폴더가 생성되고 그 하위에 'hello_java_world-1.0-SNAPSHOT.jar' 파일이 생성된 것을 확인할 수 있다.

```
<project root folder>
    |
    +-- pom.xml
    |
    +-- src
    |   |
    |   +-- main
    |       |
    |       +-- java
    |           |
    |           +-- HelloWorld.java
    |
    +-- target
        |
        +-- hello_java_world-1.0-SNAPSHOT.jar
```

생성된 'jar' 파일을 실행해보기 위해서 터미널 앱에서 'target' 폴더로 이동한 후 다음과 같이 명령어를 실행한다.

```bash
java -jar hello_java_world-1.0-SNAPSHOT.jar
```

그러면 현재의 'pom.xml' 파일 구성에서는 다음과 같은 오류 메시지가 출력될 것이다.

```
no main manifest attribute, in hello_java_world-1.0-SNAPSHOT.jar
```

프로그램 실행을 위한 'entry point'인 `main` 메쏘드를 'jar' 패키징 파일내에서 찾을 수 없다는 의미이다. 'jar' 파일을 생성할때 'entry point'를
지정해주지 않았기 때문이다. 'jar' 파일을 생성할때 'entry point'를 지정해주기 위해서는 'pom.xml' 파일에 다음과 같은 내용을 추가해주면 된다.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <version>3.3.0</version>
            <configuration>
                <archive>
                    <manifest>
                        <mainClass>HelloWorld</mainClass>
                    </manifest>
                </archive>
            </configuration>
        </plugin>
    </plugins>
</build>
```

`build` 요소를 추가해 수정한 전체 'pom.xml' 파일 내용을 편의상 한번더 붙인다.

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

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>HelloWorld</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

`<mainClass>` 요소에는 'entry point'로 사용할 클래스의 '클래스 이름'을 지정한다. '클래스 이름'은 '패키지'를 포함한 전체 이름이어야 한다.
'HelloWorld' 클래스는 '패키지'를 명시적으로 지정하지 않았기 때문에 'default package'에 위치한다. 따라서 `<mainClass>`값으로 'HelloWorld'를
지정하면된다.

`mvn package`를 재실행후 다시 생성된 'jar' 파일을 실행해보면 정상적으로 'Hello Java World!' 메시지가 콘솔에 출력되는 것을 확인할 수 있을 것이다.

생성된 'jar' 파일의 내용물을 확인하기 위해서 다음의 명령어를 실행할 수 있다.

```bash
jar tf hello_java_world-1.0-SNAPSHOT.jar
```

그러면 (적어도) 다음과 같은 내용이 출력될 것이다.

```
META-INF/
META-INF/MANIFEST.MF
HelloWorld.class
```

`v` 옵션을 추가해서 실행하면 '날짜, 파일 크기' 같은 좀더 자세한 내용을 확인할 수도 있다.

```bash
jar tvf hello_java_world-1.0-SNAPSHOT.jar
```

'.jar' 파일은 '.zip' 포맷이기 때문에 사용하는 압축툴을 이용해서 내용물을 확인할 수도 있다. '.zip' 포맷이니 물론 'jar'나 'unzip' 커맨드를 이용해서
'압축해제'를 수행할 수도 있다. `jar` 커맨드를 사용해서 압축해제는 다음과 같이한다.

```bash
jar xvf hello_java_world-1.0-SNAPSHOT.jar
```

`unzip` 커맨드를 사용해서 압축해제는 다음과 같이한다.

```bash
unzip hello_java_world-1.0-SNAPSHOT.jar
```

두 명령어 모두 '특정 파일'만을 압축해제하는 옵션도 제공한다. 예를 들어서 'META-INF/MANIFEST.MF' 파일만을 압축해제하고 싶다면 다음과 같이 `jar`
커맨드를 실행하면 된다.

```bash
jar xvf hello_java_world-1.0-SNAPSHOT.jar META-INF/MANIFEST.MF
```

명령어 실행이 완료된 후에 'META-INF/MANIFEST.MF' 파일이 생성된 것을 확인할 수 있다. `unzip` 커맨드를 사용해서도 동일한 작업을 수행할 수 있다.

```bash
unzip hello_java_world-1.0-SNAPSHOT.jar META-INF/MANIFEST.MF
```

`unzip` 명령어의 경우 `-p` 옵션을 사용하면 '특정 파일'의 내용을 로컬 파일로 저장하지 않고 표준 출력으로 확인할 수 있다. 예를 들어서
'META-INF/MANIFEST.MF'의 내용을 콘솔에서 곧바로 확인하고 싶다면 다음과 같이 실행하면 된다. 내용 확인후 별도의 파일 삭제 작업을 할 필요가 없어
편리하다.

```bash
unzip -p hello_java_world-1.0-SNAPSHOT.jar META-INF/MANIFEST.MF
```

`META-INF/MANIFEST.MF` 파일의 내용을 확인해보면 다음과 같다.

```text
Manifest-Version: 1.0
Created-By: Maven JAR Plugin 3.3.0
Build-Jdk-Spec: 21
Main-Class: HelloWorld
```

'Main-Class' 속성이 존재하고 'HelloWorld'로 지정된 것을 확인할 수 있다. 이것이 'entry point'로 지정된 클래스의 '클래스 이름'이다. `java`
커맨드로 생성된 'jar' 파일을 실행하면 대략 다음과 같은 순서의 작업이 일어난다고 볼 수 있다.

1. 'jar' 파일 내부의 'META-INF/MANIFEST.MF' 파일을 읽어서 'Main-Class' 속성값을 찾는다.
2. 'Main-Class' 속성값으로 지정된 '클래스 이름'을 찾는다. (여기서는 'HelloWorld'를 찾는다.)
3. '클래스 이름'으로 지정된 '클래스 파일'을 찾는다.
4. '클래스 파일'을 'JVM'으로 로딩한다.
5. '클래스 파일' 내부의 `main` 메쏘드를 찾는다.
6. `main` 메쏘드를 실행한다.

메이븐과 같은 빌드 툴을 사용하지 않고 '자바 (콘솔) 어플리케이션'의 'jar' 파일내 'entry point' 지정을 위해서는 'jar' 파일을 생성할때 직접 적절한
내용을 포함하는 'MANIFEST.MF' 파일을 같이 포함시켜야 했다. 메이븐과 같은 빌드 툴을 사용하여 이런 '메타'적인 파일을 직접 조작하지 않고 'pom.xml' 파일에
적절한 설정을 추가해주면 빌드 툴이 알아서 'jar' 파일내에 'MANIFEST.MF' 파일을 생성하고 그 안에 'entry point' 지정을 위한 내용을 추가해주기 때문에
여러모로 이해도 쉽고 편리하다.

