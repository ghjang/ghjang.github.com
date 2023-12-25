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

'pom.xml' 파일에 해당 프로젝트를 빌드하기 위한 설정들을 기술하게된다.

'src' 폴더 하위의 구조는 메이븐 또는 'Gradle' 빌드 툴에서 암묵적으로 가정하는 구조이다. 'Convention over Configuration'라는 개념이다.
즉, 메이븐 빌드 툴에서 사용하는 이 암묵적으로 가정하는 프로젝트 폴더 구조를 따르면 별도의 설정 없이도 쉽게 빌드를 수행할 수 있다. 이 '표준 프로젝트 폴더
구조'에 대한 보다 자세한 설명은 메이븐 공식 웹사이트의
[Introduction to the Standard Directory Layout](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)
페이지를 참고하면 된다.

참고로 이러한 관례적인 폴더 구조를 따르지 않는 (오래된) 프로젝트 소스를 메이븐으로 빌드하기 위해서는 'pom.xml'에서 `build` 요소 하위에 상황에 맞게
'소스 폴더', '리소스 폴더'등의 위치 설정을 상황에 맞게 해주어야한다. 예를들면 다음과 같은 식으로 변경할 수 있다.

```xml
<build>
    <!--
        'pom.xml'내에서 '상대 경로'로 지정된 폴더 경로는
        '프로젝트 루트 폴더(== pom.xml 파일이 위치한 폴더)'를 기준으로 한다.
        기본적으로 지정된 폴더 하위의 모든 파일들이 대상이 된다.
        
        'source' 부류 폴더 하위에는 컴파일 대상인 자바 소스 파일이 위치한다.
        컴파일되어 생성된 '.class' 파일들이 '출력 폴더(기본값 : target)'에 위치하게된다.
        '패키지' 폴더 구조가 유지된다.
        
        'resource' 부류 폴더 하위에는 컴파일 대상이 아닌 리소스 파일들이 위치한다.
        컴파일되지 않고 그대로 '출력 폴더(기본값 : target)' 하위에 복사된다.
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
                
                리소스 파일의 폴더 원래 폴더 구조를 유지하고 싶다면 다음과 같이 설정한다.
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
        컴파일된 클래스 파일과 리소스 파일이 위치할 '베이스 폴더 경로'를 설정한다.
        별도로 지정하지 않으면 'target'이 '기본값'으로 사용된다.
    -->
    <directory>out</directory>
</build>
```
