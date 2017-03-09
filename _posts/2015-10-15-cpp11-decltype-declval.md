---
title: "[C++11] decltype, std::declval"
description: ""
category: Computer Programming
tags: [C++, C++11, C++ TMP]
---

`decltype`은 C++ 언어 키워드이고 `std::declval`은 function template이다.

---

`decltype`은 **'declared type'**의 약자로 볼 수 있다. 인자로 주어진 개체(entity)나 표현식(expression)의 타입(type)을 compile-time에 알려준다. 기본적으로 다음과 같이 사용 가능하다:

```cpp
using std::is_same;

int i = 1024;

decltype(i) j = i * i;
static_assert(is_same<int, decltype(i)>::value, "");

using i_t = decltype(i);
static_assert(is_same<int, i_t>::value, "");
i_t k = j * j;

// with CLion 1.1 on OS X El Capitan
std::cout << "i: " << i << std::endl;   // 1024
std::cout << "j: " << j << std::endl;   // 1048576
std::cout << "k: " << k << std::endl;   // 0    <== THINK: how to detect something like this?

static_assert(is_same<int, decltype(1 + 1)>::value, "");
```

---

## decltype 사용에 있어서 유의해야 할 점

### 인자가 무엇이냐에 따라서 생성되는 타입이 달라진다.

기본적으로 이름이 있는 변수등에 대해서는 먼저 보인 예제 코드에서와 같이 그 변수의 원래 타입을 리턴해준다.

이름이 없는 표현식에 대해서는 lvalue일 경우 원래 타입에 `&`를 붙인 레퍼런스 타입이 리턴되고 `(p)rvalue`일 경우 원래 타입을 돌려 준다.

다음은 이름이 없는 lvalue의 예인 문자열 리터럴을 가지고 `decltype`을 테스트한 예제 코드이다. 문자열 `"abc"`의 타입은 `char const [4]`이다라는 것에 주의할 것.

```cpp
// string literal is a lvalue.
// "abc"'s type is char const [4].
char const str[4] = "abc";
char const (* pStr) [4] = &"abc";   // can get the address of the string.
char const * pStr1 = "abc";         // array to pointer decay
char const * pStr2 = str;           // array to pointer decay
//char const (* pStr3) [4] = str;   // this is a compile error
char const (* pStr4) [4] = &str;    // but this works OK.

static_assert(std::is_same<char const (&) [4], decltype("abc")>::value, "");
```
> **decay**에 대한 내용은 다음 블로그 포스트를 참고할 것: [[C++11 std::decay]](https://ghjang.github.io/computer%20programming/2015/10/19/cpp11-decay.html)

> C++17 **structured binding**으로 바인딩한 변수에 대해서는 레퍼런스 타입을 리턴한다고 함.

자세한 사항은 다음 URL을 참조하도록 할 것:

+ [cppreference decltype](http://en.cppreference.com/w/cpp/language/decltype): lambda expression으로 생성하는 객체에 대해서 컴파일러가 내부적으로 다루는 타입을 얻어내고 사용하는 예제가 추가적으로 있다. 
+ [cppreference value category](http://en.cppreference.com/w/cpp/language/value_category): lvalue, rvalue, prvalue, xvalue 같은 개념들에 대해서 설명하고 있다.

### decltype(x)와 decltype((x))의 결과 타입이 다를 수 있다.

`decltype`내부에 괄호 사용여부에 따라 표현식의 성질이 바뀐다. `()`를 사용하여 이름이 있는 변수를 감싸면 일종의 이름이 없는 표현식으로 바꾼 효과를 내서 결과적으로 다른 타입을 리턴할 수 있다. 예를 들어 이름이 있는 lvalue 변수 `x`를 `(x)`와 같이 표현하면 이름이 없는 lvalue 표현식으로 취급되어 레퍼런스 타입을 리턴한다는 것이다.

```cpp
int x = 100;
static_assert(std::is_same<int, decltype(x)>::value, "");       // decltype(x)
static_assert(std::is_same<int &, decltype((x))>::value, "");   // decltype((x))
```

### decltype에 전달된 표현식내 중첩된 서브표현식에 포함된 객체는 complete type이어야 한다.

`sizeof` 키워드와 같이 `decltype`에 전달되는 표현은 evaluation이 되는 것이 아니기 때문에 완전한 정의가 필요하지 않다. 하지만 서브표현식에 나타나는 객체에 대해서는 예외라는 것이다. 함수의 리턴 타입도 이에 해당한다.

```cpp
// declaration only
int f(int);
int g();

// with definition
int h() { return 0; }


struct CompleteType { };

struct IncompleteType;

// declaration only
template <typename T>
int doSomething(T const& t);

CompleteType returnCompleteType();
IncompleteType returnIncompleteType();

// ...

using std::is_same;

// it's ok.
static_assert(is_same<int, decltype(f(0))>::value, "");
static_assert(is_same<int, decltype(g())>::value, "");
static_assert(is_same<int, decltype(h())>::value, "");

// it's also ok.
static_assert(is_same<int, decltype(f(g()))>::value, "");
static_assert(is_same<int, decltype(f(h()))>::value, "");

// it's ok.
static_assert(is_same<int, decltype(doSomething(CompleteType()))>::value, "");

// but it's not ok. it's compile-time errors.
//decltype(doSomething(IncompleteType())) it;
//static_assert(is_same<int, decltype(doSomething(IncompleteType()))>::value, "");

// it's ok
static_assert(is_same<int, decltype(doSomething(returnCompleteType()))>::value, "");

// but it's not ok. it's compile-time errors.
//static_assert(is_same<int, decltype(doSomething(returnIncompleteType()))>::value, "");
```

---
`declval`은 선언만 있고 정의가 없다. `public` 기본 생성자가 없어 객체를 직접 생성할 수 없는 상황에서 특정 멤버함수의 리턴 타입을 얻어내는 데 사용한다고 한다:

+ [cppreference std::declval](http://en.cppreference.com/w/cpp/utility/declval)

위 레퍼런스에서 제공되는 코드보다 약간 복잡한 사용예는 다음과 같다:

```cpp
struct Default
{
    int foo() const { return 1; }
};

struct NonDefault
{
    NonDefault(NonDefault const&) { }
    long foo() const { return 1; }
};

template <typename T>
using ReturnTypeOfFoo = decltype(std::declval<T>().foo());

// ...

using std::is_same;

// it's ok.
static_assert(is_same<int, decltype(Default().foo())>::value, "");
static_assert(is_same<int, ReturnTypeOfFoo<Default>>::value, "");
static_assert(is_same<long, ReturnTypeOfFoo<NonDefault>>::value, "");

// but it's not ok. a compile error.
//static_assert(is_same<long, decltype(NonDefault().foo())>::value, "");
```

---

## 참고

+ [https://github.com/ghjang/personal_study/blob/master/cpp/decltype/main.cpp](https://github.com/ghjang/personal_study/blob/master/cpp/decltype/main.cpp)
