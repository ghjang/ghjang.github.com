# 'Hello Java World!' 콘솔 프로그램

콘솔에 'Hello Java World!'를 출력하는 자바 프로그램을 작성해보자. 먼저 'Hello Java World!'를 출력하는 자바 프로그램을 작성하기 위해서는 다음과
같이 'HelloWorld.java' 파일을 작성해야 한다.

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello Java World!");
    }
}
```

위의 내용을 'HelloWorld.java'이라는 이름의 텍스트 파일에 저장한 후 '터미널' 앱을 구동후 해당 파일의 폴더 위치에서 다음과 같이 컴파일한다.

```bash
javac HelloWorld.java
```

컴파일이 성공하면 'HelloWorld.class' 파일이 생성된다. 이제 다음과 같이 실행할 수 있다.

```bash
java HelloWorld
```

'Hello Java World!'가 콘솔에 출력되는 것을 확인할 수 있다.
