---
title: "Fold it the way you like"
description: ""
category: Computer Programming
tags: [Functional Programming, C++ TMP]
---

## What is Fold about?

**Functional Programming**의 **Fold** 개념에 대해서 언급하는 인터넷 상의 여러 C++ TMP 관련 글들을 간혹 볼 수가 있었다. 그러다가 좀 자세하게 이해한 시점은 아래의 블로그 포스트를 읽고 나서이다. 이 글에서는 수치 데이터를 다루는 **FoldRight**에 대한 설명을 포함하고있다:

+ [What Does Haskell Have to Do with C++?](http://bartoszmilewski.com/2009/10/21/what-does-haskell-have-to-do-with-c/)

Fold는 사전적인 의미로는 **접다**이다. *'접기는 뭘 접는다는 것이여?'*

Fold를 간략하게 요약하면 '입력 열을 특정 연산에 대해서 접어서 하나의 결과값을 생성(accumulation)' 하는 것이라고 할 수 있다. 좀더 단순하게 설명하면 '입력열을 받아서 하나의 결과생성' 정도로 요약할 수 있겠다.

Fold는 두가지가 있다고 한다: **FoldRight, FoldLeft**

### FoldRight

**F**를 두개의 인자를 받고 1개의 값을 리턴하는 **이항함수**라고 하자. 편의를 위해서 F를 **op(operation)**라고하자. **t0,t1,...,tn**은 함수에 인자로 전달할 값의 열이라고 하자. **init**은 어떤 초기값에 해당하는 것이라고 하자. 이상태에서 FoldRight를 추상적인 개념으로 다음과 같이 표현할 수 있다:

+ (t0 op (t1 op (t2 op ...(tn op init)...)

가만히 보면 괄호문자 **(**와 **)**가 전체 인자 열에서 우측에 우선하여 결합하는 것을 볼 수 있다. 좀 어거지일 수도 있겠으나 이런 괄호가 우측으로 **접혀진** 것처럼 보이기에 Fold라고 부르는 것이 아닐까 싶기도 하다.

op는 무엇이던지 될 수 있겠다. 흔하게드는 예가 **+**, **-** 연산이다.

### FoldLeft

먼저 설명한 FoldRight에서의 가정과 같다고 하면 FoldLeft를 추상적인 개념으로 다음과 같이 표현할 수 있다:

+ (...(init op t0) op t1) op t2)... op tn)

전체 인자 열에서 좌측에 우선하게 결합되어 있는 것을 확인할 수 있다.

특정 연산은 **교환법칙**이 성립하지 않는다. 교환칙법이 성립하는 연산의 경우는 FoldRight, FoldLeft의 결과가 동일하지만 성립하지 않는 연산의 경우는 사용에 주의해야 한다.

---

## Runtime Fold: std::accumulate

**C++ STL**에 Fold(accumulation) 기능을 하는 것이 바로 `std::accumulate` 알고리즘이다. 이항함수를 받아들이도록 오버로드된 버전은 보통 아래와 같이 구현된다:

```cpp
template<class InputIt, class T, class BinaryOperation>
T accumulate(InputIt first, InputIt last, T init, BinaryOperation op)
{
    for (; first != last; ++first) {
        init = op(init, *first);
    }
    return init;
}
```

천천히 살펴보면 이런 구현은 FoldLeft 형태임을 알 수 있다.

사용예는 다음과 같다:

```cpp
#include <numeric>

//...

std::vector<int> v = { 1, 2, 3, 4, 5 };
auto sum = std::accumulate(
                    v.begin(), v.end(),
                    0, // init
                    [](int lhs, int rhs) { return lhs + rhs; }
            );
REQUIRE(15 == sum); // refer to catch.hpp
```

---

## Compile-time Fold

나름 자체적으로 구현한 Compile-time 버전의 FoldRight와 FoldLeft 코드는 아래와 같다. 

### FoldRight

```cpp
template <typename T>
struct Identity
{
    using type = T;
};

template <typename T>
using type_is = Identity<T>;

//...

template <template <typename, typename> class F, typename init, typename... list>
struct FoldRight;

template <template <typename, typename> class F, typename init>
struct FoldRight<F, init> : type_is<init>
{ };

template <template <typename, typename> class F, typename init, typename head, typename... tail>
struct FoldRight<F, init, head, tail...>
{
    using type = typename F<head, typename FoldRight<F, init, tail...>::type>::type;
};

template <template <typename, typename> class F, typename init, typename... list>
struct FoldRight<F, init, std::tuple<list...>>
        : FoldRight<F, init, list...>
{ };
```

사용예는 다음과 같다.

```cpp
template <typename lhs, typename rhs>
struct IntegralConstantSum;

template <typename T1, T1 v1, typename T2, T2 v2>
struct IntegralConstantSum<std::integral_constant<T1, v1>, std::integral_constant<T2, v2>>
            : std::integral_constant<decltype(v1 + v2), v1 + v2>
{ };

//...

template <int i>
using int_c_t = std::integral_constant<int, i>;

//...

using sum_t = typename FoldRight<
                            IntegralConstantSum,
                            int_c_t<0>,  // init
                            int_c_t<1>,
                            int_c_t<2>,
                            int_c_t<3>,
                            int_c_t<4>,
                            int_c_t<5>
                        >::type;
static_assert(sum_t() == 15, "1 + (2 + (3 + (4 + (5 + 0)))) = 15");
```

### FoldLeft

```cpp
// NOTE: refer to the above description.

//...

template <template <typename, typename> class F, typename init, typename... list>
struct FoldLeft;

template <template <typename, typename> class F, typename last>
struct FoldLeft<F, last> : type_is<last>
{ };

template <template <typename, typename> class F, typename init, typename head, typename... tail>
struct FoldLeft<F, init, head, tail...>
        : FoldLeft<F, typename F<init, head>::type, tail...>
{ };

template <template <typename, typename> class F, typename init, typename... list>
struct FoldLeft<F, init, std::tuple<list...>>
        : FoldLeft<F, init, list...>
{ };
```

사용예는 다음과 같다.

```cpp
// NOTE: refer to the above description.

//...

using sum_t = typename FoldLeft<
                            IntegralConstantSum,
                            int_c_t<0>,  // init
                            int_c_t<1>,
                            int_c_t<2>,
                            int_c_t<3>,
                            int_c_t<4>,
                            int_c_t<5>
                        >::type;
static_assert(sum_t() == 15, "(((((0 + 1) + 2) + 3) + 4) + 5) = 15");
```

예제로든 코드는 수치계산 문제로 **+ 연산**에 대해서 예를 든 것이다. 앞서 '교환법칙' 관련하여 주의해야한다고 언급했었다. **- 연산**의 경우 FoldRight, FoldLeft의 결과가 아래와 같이 다르게 나타난다.

```cpp
template <typename lhs, typename rhs>
struct IntegralConstantSubtract;

template <typename T1, T1 v1, typename T2, T2 v2>
struct IntegralConstantSubtract<std::integral_constant<T1, v1>, std::integral_constant<T2, v2>>
        : std::integral_constant<decltype(v1 - v2), v1 - v2>
{ };

//...

{
    using subtract_t = typename FoldRight<
                                    IntegralConstantSubtract,
                                    int_c_t<0>,  // init
                                    int_c_t<1>,
                                    int_c_t<2>,
                                    int_c_t<3>,
                                    int_c_t<4>,
                                    int_c_t<5>
                                >::type;
    static_assert(subtract_t() == 3, "(1 - (2 - (3 - (4 - (5 - 0))))) = 3");
}

//...

{
    using subtract_t = typename FoldLeft<
                                    IntegralConstantSubtract,
                                    int_c_t<0>,  // init
                                    int_c_t<1>,
                                    int_c_t<2>,
                                    int_c_t<3>,
                                    int_c_t<4>,
                                    int_c_t<5>
                                >::type;
    static_assert(subtract_t() == -15, "(((((0 - 1) - 2) - 3) - 4) - 5) = -15");
}
```

---

## You can also Fold types

Compile-time Fold가 단순히 위에서든 예제와 같이 수치계산만을 한다면 그다지 흥미롭지 못하다. **C++ type list**를 '입력열값'이라고 생각하고 이에 대해서 동작할 수 있는 metafunction을 정의할 경우 재미난 type calculation을 할 수 있다.

아래는 주어진 type list에서 조건을 만족하는 type만 제거한 결과 type list를 template parameter로 가지는 `std::tuple` type을 계산해내는 `RemoveElementType` metafunction을 `FoldLeft`를 이용하여 구현한 것이다.

```cpp
template <template <typename> class Predicate, typename Tuple>
struct RemoveElementType;

template <template <typename> class Predicate, typename... list>
struct RemoveElementType<Predicate, std::tuple<list...>>
{
private:
    using init_t = std::tuple<>;

    template <typename lhsTuple, typename rhsType>
    struct AppendTypeIf;

    template <typename... lhsList, typename rhsType>
    struct AppendTypeIf<std::tuple<lhsList...>, rhsType>
            : std::conditional<
                    Predicate<rhsType>::value,
                    std::tuple<lhsList...>,
                    std::tuple<lhsList..., rhsType>
            >
    { };

public:
    using type = typename FoldLeft<AppendTypeIf, init_t, list...>::type;
};
```

FoldRight가 아닌 FoldLeft를 사용한 것은 원래 입력 리스트의 순서를 그대로 유지하기 위함이다. `RemoveElementType`의 사용예는 다음과 같다.

```cpp
#include <type_traits>
#include <tuple>

//...

using tuple_t = std::tuple<char, float, int, double, uint64_t>;
using removed_t = std::tuple<char, int, uint64_t>;
static_assert(
        std::is_same<removed_t, typename RemoveElementType<std::is_floating_point, tuple_t>::type>(),
        "removed tuple_t should be same as removed_t."
);
```

---

## 참고

+ <http://www.wolfram.com/language/elementary-introduction/29-more-about-pure-functions.html>: **Wolfram Language**의 `FoldList` 함수에 대한 설명이 포함되어있다. `FoldList`는 위에서 설명한 FoldLeft의 개념이다. 그림과 함께 설명되어있어서 Fold에 대해서 이해하는데 많은 도움을 받을 수 있다.
+ <http://www.cs.nott.ac.uk/~pszgmh/fold.pdf>: 'A tutorial on the universality and expressiveness of fold'. **Haskell**의 fold관련 설명이 포함되어있다.
+ <http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4295.html>: C++17 표준 'N4295, Folding expressions'
+ <http://en.cppreference.com/w/cpp/language/fold>: C++17 fold expression reference 
+ <https://ngathanasiou.wordpress.com/2015/12/15/182/>: 'Fun with folds'. C++17 Fold에 대한 블로그 글. 
+ [tuple_cat in a Dumb Way]({{ site.baseurl }}{% post_url 2016-02-11-tuple_cat-in-a-dumb-way %}): C++17 fold expression 관련 내용 일부를 포함한 다른 블로그 포스트이다.
+ 사용한 예제 코드
    - <https://github.com/ghjang/rocky/blob/master/rocky/skull/FoldL.h>
    - <https://github.com/ghjang/rocky/blob/master/rocky/skull/FoldR.h>
    - <https://github.com/ghjang/rocky/blob/master/rocky/test/skull/FoldLTest.cpp>
    - <https://github.com/ghjang/rocky/blob/master/rocky/test/skull/FoldRTest.cpp>
    - <https://github.com/ghjang/rocky/blob/master/rocky/app/RemoveIf.h>
    - <https://github.com/ghjang/rocky/blob/master/rocky/test/app/RemoveIfTest.cpp>
