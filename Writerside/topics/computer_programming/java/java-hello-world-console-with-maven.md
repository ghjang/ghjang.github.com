# Maven 빌드 설정 구성

'Hello Java World!' 콘솔 프로그램을 Maven 빌드 툴로 빌드하기 위해서는 Maven 프로젝트 구조를 구성해야 한다. Maven 빌드 툴에서 암묵적으로 가정하는
프로젝트 구조는 대략 다음과 같다.

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

'pom.xml' 파일에 해당 프로젝트를 빌드하기 위한 설정을 기술한다.

'src' 폴더 하위의 구조는 Maven 또는 Gradle 빌드 툴에서 암묵적으로 가정하는 구조이다. 'Convention over Configuration'라는 개념이다.
즉, Maven 빌드 툴에서 사용하는 이 암묵적으로 가정하는 프로젝트 구조를 따르면 별도의 설정 없이도 빌드를 수행할 수 있다.

이러한 관례적인 폴더 구조를 따르지 않는 (오래된) 프로젝트 소스를 메이븐으로 빌드하기 위해서는 'pom.xml'에서 `build` 요소 하위에 상황에 맞게
'소스 폴더', '리소스 폴더'등의 위치 설정을 해주어야한다. 예를들면 다음과 같다.

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
        <resource>
            <directory>src</directory>
        </resource>
    </resources>
    <testSourceDirectory>test</testSourceDirectory>
    <testResources>
        <testResource>
            <directory>test</directory>
        </testResource>
    </testResources>
</build>
```
