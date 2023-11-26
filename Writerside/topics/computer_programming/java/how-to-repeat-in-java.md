# 자바 언어에서 '반복'하는 방법

## for문

```java
for (int i = 0; i < 10; ++i) {
    System.out.println(i);
}
```

## while문

```java
int i = 0;
while (i < 10) {
    System.out.println(i);
    ++i;
}
```

## do-while문

```java
int i = 0;
do {
    System.out.println(i);
    ++i;
} while (i < 10);
```

## 'enumeration'을 이용한 '컬렉션' 객체 순회

`Enumeration`은 '자바 1'부터 지원하는 인터페이스이다. '컬렉션' 객체를 순회할 때 사용한다. 아래 예시 코드의 `Vector` 클래스는
`Enumeration` 인터페이스를 구현한다.

```java
Vector<String> vector = new Vector<>();
vector.add("a");
vector.add("b");
vector.add("c");
vector.add("d");
vector.add("e");
Enumeration<String> enumeration = vector.elements();
while (enumeration.hasMoreElements()) {
    System.out.println(enumeration.nextElement());
}
```

## 'iterator'를 이용한 '컬렉션' 객체 순회

`Iterable`은 '자바 2'부터 지원하는 인터페이스이다. '컬렉션' 객체를 순회할 때 사용한다. 아래 예시 코드의 `ArrayList` 클래스는
`Iterable` 인터페이스를 구현한다.

```java
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
list.add("e");
Iterator<String> iter = list.iterator();
while (iter.hasNext()) {
    System.out.println(iter.next());
}
```

`Iterable` 인터페이스는 `Enumeration` 인터페이스보다 나중에 나온 보다 개선된 인터페이스이다. 따라서 가능하면 `Enumeration` 대신 `Iterable`을
사용하는 것이 바람직하다.

## 'enhanced' for문

'enhanced for loop' 또는 'for each loop'라고도 불리는 이 문법은 '자바 5'부터 지원하는 문법이다. '컬렉션' 객체를 순회할 때 사용한다.

```java
int[] arr = { 1, 2, 3, 4, 5 };
for (int i : arr) {
    System.out.println(i);
}
```

enhanced for loop는 'iterator'를 이용한 '컬렉션' 객체 순회와 'enumeration'을 이용한 '컬렉션' 객체 순회를 대체할 수 있다.
'배열'은 `Iterable` 인터페이스를 구현하지 않지만 enhanced for loop에서 사용할 수 있다.
