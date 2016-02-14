---
layout: post
title: "[C++11] std::decay"
description: "std::decay에 대해서 알아본다."
category: Computer Programming
tags: [C++, C++11, C++ TMP]
---
{% include JB/setup %}

decay는 사전적으로 '부식'이라는 의미이다. C++에서 decay란 특정 context에서 type이 원래 고유의 것이 아닌 다른 type으로 변질되는 것을 말한다. std::decay는 decay가 일어나는 문맥에서의 type을 계산해주는 meta function.

---

## array-to-pointer decay
가장 대표적인 decay의 예가 array-to-pointer decay이다:
```cpp
using std::is_same;
using std::decay;   // C++11
using std::decay_t; // C++14

// ...

// int array to pointer decay
int a[3] = { 1, 2, 3 };
int * pa = a;
static_assert(is_same<decltype(a), int[3]>::value, "");
static_assert(is_same<decltype(pa), int *>::value, "");
static_assert(is_same<decltype(pa), decay<decltype(a)>::type>::value, "");
static_assert(is_same<decltype(pa), decay_t<decltype(a)>>::value, "");
```

array-to-pointer decay가 일어난 문맥에서는 배열의 최상위 차원 크기를 알 수 없게된다. 1차원 배열이 가장 많이 사용되게되는데, 포인터 변수만을 놓고 보면 해당 포인터가 가리키는 것이 단일객체인지 배열인지 알 수 없다. 보통 배열과 배열크기정보를 같이 전달하여 처리하는 패턴이 된다. 전되달는 크기정보가 잘못되었거나 범위를 넘어가는 인덱스 조작을 통해서 문제가 발생할 수 있게된다.

다차원 배열에서의 decay 예는 다음과 같은 형태가 된다:
```cpp
// multi-dimensional array to pointer decay
int aa[2][3] = {
		{ 1, 2, 3 },
		{ 4, 5, 6 }
};
int (* paa) [3] = aa; // loses top-level dimension size information, 2.
static_assert(is_same<decltype(aa), int [2][3]>::value, "");
static_assert(is_same<decltype(paa), int (*) [3]>::value, ""); // pointer to 'int [3]'
paa[1][0] = 44;
assert(aa[1][0] == 44);
```

---

## function-to-pointer decay
늘상 사용하는 당연히 생각되는 또다른 decay는 function-to-pointer decay이다. C++에는 function pointer type이외에 function type과 function reference type이 존재한다. function pointer type은 알아도 나머지 것들에 대해서 잘 생각하지 못하는 경우들이 있다. 함수는 int와 같은 primitive type처럼 함수의 인자등으로 전달될 수 있는 first class type이라서 '&', '*' 등을 붙여서 compound type을 구성하는 것이 가능하다.
```cpp
int f(int i) { return i + 1; }

// ...

auto pf = f;
int (&rf) (int) = f;
int (*pf1) (int) = f;
static_assert(is_same<int (int), decltype(f)>::value, "");                  // function type
static_assert(is_same<int (&) (int), decltype(rf)>::value, "");             // function reference type
static_assert(is_same<int (*) (int), decltype(pf)>::value, "");             // function pointer type
static_assert(is_same<int (*) (int), decay<decltype(f)>::type>::value, "");
static_assert(is_same<int (*) (int), decay_t<decltype(f)>>::value, "");
```

---

## Function template에 인자로 전달시 decay
template을 통한 generic한 코드를 작성시에는 이런 decay 상황에 대해서 대비해야 할 것이다. 원하지 않는 함수가 호출 되거나, 제대로 동작하지 않거나 하는 상황이 발생할 수 있을 것이다. 적절한 함수 오버로딩을 추가하거나, type trait과 std::enable_if와 같은 도구를 활용해야할 필요가 있을지도 모르겠다.
```cpp
template <typename T>
auto ReturnDecayedValue(T t)
{
    return t;
}

template <typename T>
auto ReturnDecayedValue1(T t) -> decltype(t)
{
    return t;
}

template <typename T>
auto ReturnDecayedValue2(T & t)
{
    return t;
}

template <typename T>
auto ReturnDecayedValue3(T & t) -> decltype(t)
{
    return t;
}

// ...

int f(int i) { return i + 1; }

// ...

int a[3] = { 1, 2, 3 };

// ...

int aa[2][3] = {
		{ 1, 2, 3 },
		{ 4, 5, 6 }
};

// ...

static_assert(is_same<int *, decltype(ReturnDecayedValue(a))>::value, "");
static_assert(is_same<int (*) [3], decltype(ReturnDecayedValue(aa))>::value, "");
static_assert(is_same<int (*) (int), decltype(ReturnDecayedValue(f))>::value, "");

static_assert(is_same<int *, decltype(ReturnDecayedValue1(a))>::value, "");
static_assert(is_same<int (*) [3], decltype(ReturnDecayedValue1(aa))>::value, "");
static_assert(is_same<int (*) (int), decltype(ReturnDecayedValue1(f))>::value, "");

// ???
static_assert(is_same<int *, decltype(ReturnDecayedValue2(a))>::value, "");
static_assert(is_same<int (*) [3], decltype(ReturnDecayedValue2(aa))>::value, "");
static_assert(is_same<int (*) (int), decltype(ReturnDecayedValue2(f))>::value, "");

static_assert(is_same<int (&) [3], decltype(ReturnDecayedValue3(a))>::value, "");       // reference to 'int [3]'
static_assert(is_same<int (&) [2][3], decltype(ReturnDecayedValue3(aa))>::value, "");   // reference to 'int [2][3]'
static_assert(is_same<int (&) (int), decltype(ReturnDecayedValue3(f))>::value, "");     // reference to 'int (int)'
```

array에도 (당연한 것인데) 'array type, array reference type, array pointer type'이 있다는 것을 기억하는게 좋겠다.

---

### 참고
+ [cppreference std::decay](http://en.cppreference.com/w/cpp/types/decay): 좀더 세부적인 내용에 대해서 확인하도록 할 것. implicit conversion 관련 내용의 링크도 내용 파악하는 것이 좋을 것임.
+ [https://github.com/ghjang/personal_study/blob/master/cpp/decay/main.cpp](https://github.com/ghjang/personal_study/blob/master/cpp/decay/main.cpp)
