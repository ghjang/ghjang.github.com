# 자바 코딩 컨벤션

## 1. '클래스 이름'은 '대문자'로 시작한다.

```java
public class MyClass {
    // ...
}
```

## 2. '메소드 이름'은 '소문자'로 시작한다.

```java
public class MyClass {
    public void myMethod() {
        // ...
    }
}
```

## 3. '변수 이름'은 '소문자'로 시작한다.

```java
public class MyClass {
    public void myMethod() {
        int myVar = 0;
    }
}
```

## 4. '상수 이름'은 '대문자'로 시작한다.

```java
public class MyClass {
    public static final int MY_CONST = 0;
}
```

## 5. '패키지 이름'은 '소문자'로 시작한다.

```java
package com.example.myapp;
```

## 6. '파일 이름'은 '클래스 이름'과 같다.

```java
// MyClass.java
public class MyClass {
    // ...
}
```

## 7. '낙타식 표기법(camel case)'을 사용한다.

```java
public class MyClass {
    public void myMethod() {
        int myVar = 0;
    }
}
```

경우에 따라서는 '스네이크 표기법(snake case)'을 사용하기도 한다.

```java
public class MyClass {
    public void my_method() {
        int my_var = 0;
    }
}
```
