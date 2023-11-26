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

'정수' 값 따위를 입력받을 때는 `nextInt()` 메소드를 사용한다.

```java
Scanner scanner = new Scanner(System.in);
int input = scanner.nextInt();
```
