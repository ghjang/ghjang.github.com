---
title: "dot-dot-dot is everywhere."
description: ""
category: Computer Programming
tags: [C++, C++ TMP]
---

C++에 관심이 좀 있다면 "`...`" 표기를 C++11부터 도처에서 볼 수 있다. 여기서는 머리 속 정리도 할 겸 "`...`" 표기가 얼마나 많이 C++에서 사용되고 있는지 나름대로 정리해 본다.

---

## Variadic Arguments

C를 배워본 사람이라면 `Hello World!`를 `printf`로 출력해 보았을 것이다. 함수 원형은 다음과 같다.

```c
int printf( const char* format, ... );
```

여기서 마지막 인수 부분 `...`은 **가변 인수**다. 즉 *0개 이상의 인자*를 지정할 수 있다. 아래와 같은 호출이 가능하다.

```c
printf("Hello World!");
printf("Hello %s World!", "Wonderful");
printf("Hello %s World%c", "Wonderful", '!');
printf("Hello %d %s World%c", 2, "Wonderful", '!');
```

가변 인수를 처리하는 아래 함수 구현 예제는 cppreference 사이트에서 가져와 붙였다.

```cpp
#include <iostream>
#include <cstdarg>
 
int add_nums(int count, ...) 
{
    int result = 0;
    va_list args;
    va_start(args, count);
    for (int i = 0; i < count; ++i) {
        result += va_arg(args, int);
    }
    va_end(args);
    return result;
}
 
int main() 
{
    std::cout << add_nums(4, 25, 25, 50, 50) << '\n';
}
```

위 코드에서 `add_nums` 함수는 *0개 이상*의 `int` 인수를 받아서 모두 더하는 코드라고 볼 수 있다. 코드에서 `va_list`, `va_start`, `va_arg`, `va_end`를 사용해 첫 번째 매개변수 `count` 이후에 넘어올 가변인수가 `int`타입으로 `count`개 있다고 정해놓고 처리하는 코드다.

`printf` 함수에 구현에 대해 잠시 생각해보면 첫번째 인수는 약속한 포맷팅용 문자열이 오고, 이후는 이 포맷팅용 문자열내에서 *place holder*로 사용된 부분을 대체할 실제 인수가 온다. 포맷팅 문자열 파싱후 어떤 타입의 인수가 몇개가 와야 하는지 파악해 가변 인수를 처리하는 식일 것이다.

### Syntax Notes

C에서는 `add_nums`, `printf` 예제 함수 원형에서 볼 수 있는 것처럼 첫번째 매개변수는 이름이 있어야 하고 이후에 가변 인수 지정 표기가 올 수 있다. C++의 경우는 이런 제약이 없다. 다음과 같은 함수를 선언하는 것이 가능하다:

```cpp
int printz(...);
```

이런 식의 코드는 함수 구현에서 몇개의 인자가 오는지 알 수 없기 때문에 제대로 인자를 처리할 수가 없다. 함수 구현자와 호출자가 어떤 타입의 몇개 인자를 보내고 받을지 사전에 정하고 사용하면 되겠으나, 이렇게 하면 가변 인자를 사용하는 의미가 없다.

C++에서는 이런 식의 함수 정의를 **SFINAE**와 관련해 **메타프로그래밍**에서 사용하기도 한다. *overload resolution rule*상에서 `...`의 매치 우선 순위가 가장 낮다는 점을 활용한다. 이해와 편의를 위해 역시 cppreference 사이트의 예제 코드를 붙였다.

```cpp
template<typename T>
class is_class {
    typedef char yes[1];
    typedef char no [2];
    template<typename C> static yes& test(int C::*); // selected if C is a class type
    template<typename C> static no&  test(...);      // selected otherwise
  public:
    static bool const value = sizeof(test<T>(0)) == sizeof(yes);
};
```

예제의 `is_class` 메타함수의 사용예는 다음과 같다:

```cpp
struct S
{ };

static_assert(is_class<S>::value);
static_assert(!is_class<int>::value);
```

이런 식의 메타프로그래밍 이외에 어떤 오버로딩된 함수 집합의 *fallback* 작업을 정의하기 위한 용도로도 사용할 수 있을 것이다.

추가로 C++에서는 다음 두 문법이 모두 허용된다. 둘다 동일한 의미다:

```cpp
int f(int n, ...);
int g(int n...);
```

C++ 역사적으로 콤마를 사용하지 않는 방법이 먼저 도입된 후에 콤마가 붙은 C 스타일 문법이 추가되었다고 한다. 코드 호환성을 위해 두 문법을 다 유지하기로 결정했다고 한다.

> 좀더 자세한 사항은 다음을 참고하라: <http://en.cppreference.com/w/cpp/language/variadic_arguments>

### Can't be used with `__stdcall` calling convention

`__stdcall` 함수 호출 규약의 함수는 함수쪽에서 스택 클린업을 수행하게 코드가 생성되기 때문에 가변 인수를 사용할 수 없다. 컴파일러가 함수 자체에 대한 코드 생성시에 인자가 몇개 있는지 컴파일 타임에 계산할 수가 없기 때문에 스택 클린업 코드를 생성할 수 없다.

---

## Variadic Template, Parameter Pack and Parameter Pack Expansion

C++11부터는 템플릿과 관련해 새로운 문법이 추가되어 *n개*의 인수를 다룰 수 있게 되었다. 앞서 설명한 가변 인수와 구분해서 기억해야할 사항은 여기서 의미하는 n개라는 수는 특정 템플릿이 인스턴스화되는 컴파일 시점에 정해진다. 즉 *런타임*에 가변이 아니라 *컴파일 타임*에 템플릿에 넘기는 인자가 가변일 수 있다는 것이다.

`printf`를 예로 들어 설명하면 `printf`를 호출하는 클라이언트 코드 자체에서는 `printf`에 넘기는 인자의 수를 컴파일 시간에 알 수 있다. 하지만 가변 인수를 사용하는 `printf` 구현 자체에서는 이런 컴파일 타임 정보를 이용하지 않고 *런타임*에서 포맷팅 문자열을 파싱해 전달되는 인자의 수를 *계산*한다.

여기서는 n개의 주어진 수를 합해서 리턴해주는 간단한 코드를 예로 들어 템플릿 관련 코드에서의 `...` 표기 사용을 설명하겠다.

> 설명하고자 하는 것이 템플릿 자체가 아니라 `...`의 사용에 있기에 가능하면 타입 적합성 체크나 타입 계산 코드는 예제에서 배제했다.

### function template

```cpp
#include <type_traits>

template <typename... T>
constexpr auto sum(T... n)
{
    using element_t = std::common_type_t<T...>;

    element_t tempArr[] = { n... };
    element_t sumVal = 0;
    for (auto t : tempArr) {
        sumVal += t;
    }
    return sumVal;
}

static_assert(sum(1, 2, 3, 4, 5) == 15);
```

여기서 `typename... T` 부분을 **parameter pack**이라고 한다. parameter pack에 담긴 정보를 사용하기 위해 *풀어헤치는 작업*을 **Parameter Pack Expansion**이라고 한다. 예제에서 `T...`과 `n...`부분이 parameter pack expansion 표현이다. `T...`의 경우는 parameter pack으로 부터 *type*정보를 꺼내오는 것이고, `n...`은 대응되는 *value*값을 꺼내오는 것이다.

이 `sum` 함수의 경우 **C++17 fold expression** 문법을 적용하면 구현이 매우 단순해 진다:

```cpp
template <typename... T>
constexpr auto sum(T... n)
{
    return (... + n);
}
```

이 버전의 구현에서 `(... + n)`과 같은 부분을 fold expression이라고 부른다. 보다시피 여기서도 `...`을 볼 수가 있다.

> **fold**는 Functional Programming에서 이야기하는 바로 그것이다. fold 연산 자체에 대한 설명은 여기서는 하지 않는다. 검색을 하거나 다른 포스트 [Fold it the way you like](https://ghjang.github.io/computer%20programming/2015/12/12/about-fold.html)를 참고하라.

템플릿의 인수에 넘길 수 있는 *non-type template argument*에는 *정수 상수*가 있기 때문에 (그다지 유용하지 않겠지만) 다음처럼 `sum`을 구현할 수도 있다:

```cpp
#include <type_traits>

template <int... n>
constexpr auto sum()
{
    return (... + n);
}

static_assert(sum<1, 2, 3, 4, 5>() == 15);
```

이 변경된 예제에서 `int... n` 부분이 parameter pack이다. 보다시피 이미 정해진 `int`같은 타입으로도 pack을 표현할 수 있다. 이 경우 *타입* 정보는 이미 알고 있으니 풀어헤칠 필요가 없고 풀어헤칠 수도 없다. *값* 부분만 확장해서 사용할 수 있겠다.

### class template

함수 템플릿으로 구현한 `sum` 예제를 클래스 템플릿을 사용해 *수치 메타함수* 예제를 작성하겠다. 역시 `...` 표기에 대한 설명용이지 코드 자체에 그다지 유용한 의미가 있지는 않다.

```cpp
#include <type_traits>

template <int... n>
struct sum
{
    constexpr static auto value = (... + n);
};

static_assert(sum<1, 2, 3, 4, 5>::value == 15);
```

`int... n` parameter pack의 타입 정보를 `int`로 고정하지 않고 다음처럼 컴파일 타임에 결정되는 *type*으로 변경하면 컴파일 에러가 발생한다.

```cpp
template <typename... n>
struct sum
{
    constexpr static auto value = (... + n);
};
```

`n`은 타입 자체이기에 `+` 연산을 할 수가 없다. 이런 수치계산 문제를 해결하는 *idiom*이 있는데 다음과 같은 식이다:

```cpp
#include <type_traits>

template <typename... n>
struct sum;

template <typename T, T... n>
struct sum<std::integral_constant<T, n>...>
{
    constexpr static auto value = (... + n);
};

static_assert(
    sum<
        std::integral_constant<int, 1>,
        std::integral_constant<int, 2>,
        std::integral_constant<int, 3>,
        std::integral_constant<int, 4>,
        std::integral_constant<int, 5>
    >::value == 15
);
```

(*타입 계산*이라는 메타프로그래밍적 개념에 익숙하지 않다면 *이게 뭔 뻘짓인가?*라고 생각할지도 모르겠다.)

> 좀더 자세한 사항은 다음을 참고하라:
> * <http://en.cppreference.com/w/cpp/language/parameter_pack>

---

## Variadic Template + Variadic Arguments

---

## Variadic Macro

---

## Conclusion

