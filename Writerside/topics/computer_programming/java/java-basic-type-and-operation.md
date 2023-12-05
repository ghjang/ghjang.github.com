# 기본 타입과 연산

## 기본 타입

자바의 '기본(primitive) 타입'에는 다음과 같은 타입들이 있다:

1. 정수 타입: `byte`, `short`, `int`, `long`
2. 실수 타입: `float`, `double`
3. 문자 타입: `char`
4. 논리 타입: `boolean`

각 타입은 다음과 같은 크기를 가진다:

- `byte`: 8비트
- `short`: 16비트
- `int`: 32비트
- `long`: 64비트
- `float`: 32비트
- `double`: 64비트
- `char`: 16비트
- `boolean`: 가상 머신에 따라 다름

이들은 모두 '숫자'를 표현하는 데 사용되며, `boolean`은 참/거짓 값을 표현하는 데 사용되고, `char`는 유니코드 문자를 표현하는 데 사용된다.

'C#'과는 다르게 자바에서 기본 타입들은 '객체'가 아니다. 즉 '암묵적'으로 `Object` 클래스를 상속하지 않는다. 따라서, 기본 타입들은 아무런 '메소드'를
가지지 않는다. 예를 들어, 다음과 같은 코드는 컴파일 오류가 발생한다.

```java
int i = 10;
System.out.println(i.toString()); // 컴파일 오류
```

반면에 'C#'에서는 다음과 같은 코드가 가능하다.

```C#
int i = 10;
Console.WriteLine(i.ToString()); // 10
```

자바의 기본 타입들은 `Object`를 상속하지 않기 때문에 다음과 같은 코드도 당연히 컴파일 오류를 발생시킨다.

```java
int i = 10;
System.out.println(i instanceof Object); // 컴파일 오류
i.getClass(); // 컴파일 오류
```

참고로 'C#'에서는 다음과 같은 코드가 가능하다.

```C#
int i = 10;
Console.WriteLine(i is object); // True
Console.WriteLine(i.GetType()); // System.Int32
```

## 부호없는 숫자 타입

자바에는 '부호없는(unsigned) 숫자 타입'이 없다. 자바의 모든 정수 타입(`byte`, `short`, `int`, `long`)은 부호가 있다. 하지만, `char` 타입은
`0`부터 `65535`까지의 '부호 없는 16비트 유니코드 값'을 저장할 수 있다.

### `char` 타입

`char` 타입은 부호가 없는 숫자 타입이라고 생각할 수도 있지만, 사실은 문자를 표현하기 위한 타입이다. 따라서, `char` 타입에 저장된 값을 숫자로 사용할
수는 있지만, 문자로 사용하는 것이 더 적합하다. 예를 들어, 다음과 같은 코드는 문자를 출력한다.

```java
char c = 'A';
System.out.println(c); // A
```

하지만, 다음과 같이 `char` 타입의 값을 숫자로 사용할 수도 있다.

```java
char c = 'A';
int i = c;
System.out.println(i); // 65
```

`char` 타입의 값을 숫자로 사용할 때는 주의해야 한다. `char` 타입의 값을 숫자로 사용할 때는 `int` 타입으로 변환된다. 따라서, `char` 타입의 값을
`byte`나 `short` 타입의 변수에 저장하려면 캐스팅을 사용해야 한다. 예를 들어, 다음과 같은 코드는 컴파일 오류가 발생한다.

```java
char c = 'A';
byte b = c; // 컴파일 오류
short s = c; // 컴파일 오류
```

다음과 같이 캐스팅을 사용하면 컴파일 오류를 피할 수 있다.

```java
char c = 'A';
byte b = (byte)c;
short s = (short)c;
```

'영문 알파벳' 문자에 대해서 `char` 타입의 값을 숫자로 취급한 후 `+` 연산을 수행하면, 문자의 다음 문자를 얻을 수 있다. 예를 들어, 다음과 같은 코드는
`'A'` 문자의 다음 문자인 `'B'`를 얻는다.

```java
char c = 'A';
char next = (char)(c + 1);
System.out.println(next); // B
```

Java 8부터 `Integer`와 `Long` 클래스는 부호 없는 연산을 지원하는 메소드가 제공되긴 한다. 예를 들어, `divideUnsigned`, `compareUnsigned`
등의 메소드를 사용하여 부호 없는 연산을 수행할 수 있다. 이는 주로 비트 연산이나 해시 코드 계산 등에서 유용하게 사용된다. 다음과 같이 사용할 수 있다.

```java
int i = Integer.parseUnsignedInt("4294967295");
System.out.println(i); // 4294967295
System.out.println(Integer.toUnsignedString(i)); // 4294967295
System.out.println(Integer.divideUnsigned(i, 2)); // 2147483647
```

## 숫자 타입 연산의 결과값 프로모션 규칙

자바에서는 숫자 타입 연산의 결과값을 프로모션(promotion) 규칙에 따라 타입을 변환한다. 프로모션 규칙은 다음과 같다.

1. 피연산자의 타입이 `double`이면, 다른 피연산자의 타입을 `double`로 변환하고 연산을 수행한다.
2. 피연산자의 타입이 `float`이면, 다른 피연산자의 타입을 `float`로 변환하고 연산을 수행한다.
3. 피연산자의 타입이 `long`이면, 다른 피연산자의 타입을 `long`로 변환하고 연산을 수행한다.
4. 피연산자의 타입이 `int`이면, 다른 피연산자의 타입을 `int`로 변환하고 연산을 수행한다.
5. 피연산자의 타입이 `char`, `byte`, `short`이면, `int`로 변환하고 연산을 수행한다.

'5'번 항목은 유의할 필요가 있다. 예를 들어, `byte` 타입과 `byte` 타입의 연산 결과는 `byte`가 아니라 int` 타입으로 변환된다. 따라서, 다음과 같은
코드는 컴파일 오류 이다.

```java
byte b1 = 10;
byte b2 = 20;
byte b3 = b1 + b2; // 컴파일 오류
```

위 코드는 다음과 같이 수정해야 한다.

```java
byte b1 = 10;
byte b2 = 20;
int b3 = b1 + b2;
```

또는 다음과 같이 캐스팅을 사용할 수도 있다.

```java
byte b1 = 10;
byte b2 = 20;
byte b3 = (byte)(b1 + b2);
```
