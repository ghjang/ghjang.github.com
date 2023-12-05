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
