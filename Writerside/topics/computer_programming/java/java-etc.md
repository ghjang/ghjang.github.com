# ...

## '콘솔'에 '줄바꿈'만 출력
              
```java
System.out.println();
```

## '콘솔'에서 '일시중지'

```java
System.in.read();
```

아무런 입력을 받기 전까지 '일시중지'되는 효과를 낸다.

## 'Scanner'를 이용한 '콘솔' 입력

`java.util.Scanner` 클래스를 이용하면 '콘솔'에서 입력을 그냥 `System.in`으로 직접 받아 처리하는 것보다 편리하다.

```java
Scanner scanner = new Scanner(System.in);
String input = scanner.nextLine();
```

'정수' 값 따위를 입력받을 때 사용 가능한 메쏘드가 있다. 예를 들어서 정수 값을 읽기 위한 `nextInt()` 메소드가 있다.

```java
Scanner scanner = new Scanner(System.in);
int input = scanner.nextInt();
```

`Scanner`는 콘솔에서의 '공백' 문자를 기준으로 입력을 구분한다. 예를 들어서 `1 2 3`을 입력하면 `nextInt()` 메소드를 세 번 호출하면서
각각 `1`, `2`, `3`을 읽어들인다. 물론 '정수'와 '문자열'을 섞어서 입력할 수도 있다. 입력에서 읽어 들이려는 데이터 형식에 맞는 메소드를 차례대로 호출하면
각 형식에 맞는 값을 읽어들일 수 있다. 예를 들어서 `1 2 3 abc`를 입력하면 `nextInt()` 메소드를 세 번 호출하면서 각각 `1`, `2`, `3`을 읽어들이고
`next()` 메소드를 호출하면서 `abc`를 읽어들인다.

## `switch' 제어문에서 '문자열' 사용

'JDK 7'부터는 `switch` 제어문에서 '문자열'을 사용할 수 있다.

```java
String str = "abc";
switch (str) {
case "abc":
    System.out.println("abc");
    break;
case "def":
    System.out.println("def");
    break;
default:
    System.out.println("default");
    break;
}
```

## 깊게 중첩된 '반복문' 탈출

`break` 문을 사용하여 깊게 중첩된 반복문을 탈출할 수 있다. 그렇게 하려면 반복문에 '레이블'을 붙여야 한다. 예를 들어서 다음과 같이 `outer`라는
레이블을 붙여서 `break` 문을 사용하면, `outer` 레이블이 붙은 반복문을 탈출한다.

```java
outer: for (int i = 0; i < 10; i++) {
    for (int j = 0; j < 10; j++) {
        if (i == 5 && j == 5) {
            break outer;
        }
    }
}
```

## 가비지 컬렉션

'가비지 컬렉션(garbage collection)'은 '힙' 영역에 있는 객체 중에서 더 이상 사용되지 않는 객체를 찾아서 자동으로 메모리에서 제거하는 기능이다.
'가비지 컬렉션'은 '힙' 영역에 있는 객체 중에서 '참조되지 않는 객체'를 찾아서 제거한다. 가비지 컬렉션이 실행되는 시점은 'JVM'이 판단한다. 정확히 언제
실행되는지는 알 수 없다. 가비지 컬렉션이 실행되는 동안에는 'JVM'이 멈추기 때문에, 가비지 컬렉션이 자주 실행되면 프로그램의 성능이 저하될 수 있다.

참고로 가비지 컬렉션을 실행하는 시점을 개발자가 직접 지정할 수도 있다. `System.gc()` 메소드를 호출하면 가비지 컬렉션을 실행한다. 하지만 이 메소드를
호출해도 가비지 컬렉션이 바로 실행되는 것은 아니다. `System.gc()` 메소드를 호출하면 가비지 컬렉션을 실행하도록 요청하는 것이다. 따라서 가비지 컬렉션이
실행되는 시점은 여전히 'JVM'이 판단한다.

`System.runFinalization()` 메소드를 호출하면 '종료자(finalizer)' 메쏘드를 실행한다. '종료자'는 객체가 소멸되기 전에 실행되는 메소드이다.
'종료자'는 `Object` 클래스의 `finalize()` 메소드를 오버라이딩하여 구현한다. `System.runFinalization()` 메소드를 호출하면 '종료자'를 실행하도록
요청하는 것이다. 따라서 '종료자'가 실행되는 시점은 여전히 'JVM'이 판단한다. `finalize()` 메소드는 'JDK 9'부터는 'deprecated'되었다.
'deprecated'된 메소드는 사용하지 않는 것이 좋다. 'JDK 9' 이전에는 다음과 같이 `finalize()` 메소드를 오버라이딩하여 '종료자'를 구현했다.

```java
@Override
protected void finalize() throws Throwable {
    // 종료자 구현
}
```

`finalize()` 메소드에 구현했던 내용 중에는 '파일'이나 '네트워크 연결' 등의 시스템 리소스를 닫아 주는 것이 있었다. '파일'이나 '네트워크 연결' 등의
시스템 리소스는 'JVM'이 자동으로 닫아주지 않기 때문에 '종료자'를 이용하여 닫아주었다. 하지만 'JDK 7'부터는 'try-with-resources' 문을 사용하여
누수가 있을 수 있는 리소스를 닫아주는 것이 권장된다. 'try-with-resources' 문을 사용하면 리소스를 명시적으로 닫아주는 코드를 별도로 작성하지 않아도
된다. 'try 블럭'이 종료될 때 자동으로 '파일'이나 '네트워크 연결' 등의 리소스를 닫아 주기 위한 메쏘드인 `close()`가 자동으로 호출된다. 이러한 목적으로
'try-with-resources' 문을 사용하면 '종료자'를 구현할 필요가 없고 보다 간결하며 효과적인 코드를 작성할 수 있다.

```java
try (FileInputStream fis = new FileInputStream("file.txt")) {
    // 파일 읽기
} catch (IOException e) {
    // 예외 처리
}
```

'try-with-resources' 문을 사용하려면 리소스를 표현하는 클래스가 `AutoCloseable` 인터페이스를 구현해야 한다. `AutoCloseable` 인터페이스는
`close()` 메소드를 하나만 가지고 있다. `close()` 메소드는 보통 특정 리소스를 닫아주는 용도의 메소드이다. `Closeable` 인터페이스는
`AutoCloseable` 인터페이스를 확장한 것으로 '메소드 선언 특화(method declaration specialization)'한다. 두 인터페이스 모두 `close()`
메소드를 가지고 있지만, `AutoCloseable` 인터페이스는 `close()` 메소드가 `throws Exception`을 선언하고 있지만, `Closeable` 인터페이스는
`throws IOException`을 선언하고 있다. 코드로 보면 다음과 같다.

```java
public interface AutoCloseable {
    void close() throws Exception;
}

// ...

public interface Closeable extends AutoCloseable {
    void close() throws IOException;
}
```

코드에서 보다시피 `Closeable`은 'IO' 작업과 관련된 컨텍스트에서 사용하기에 좀더 적합하다.


## 기타

1. '스택 프레임'에 대한 설명이 필요하다.
2. 자바 객체 생성 후에 변수에 실제 저장되는 값이 무엇인지 설명이 필요하다. '객체 id'라고 부르는 것 같은데, '객체 id'가 무엇인지 설명이 필요하다.
3. 