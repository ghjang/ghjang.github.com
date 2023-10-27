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

```c++
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

위 코드에서 `add_nums` 함수는 *0개 이상*의 `int` 인수를 받아서 모두 더하는 코드라고 볼 수 있다. 코드에서 `va_list`, `va_start`, `va_arg`, `va_end`를 사용해 첫 번째 매개변수 `count` 이후에 넘어올 가변 인수가 `int`타입으로 `count`개 있다고 정해놓고 처리하는 코드다.

`printf` 함수에 구현에 대해 잠시 생각해보면 첫번째 인수는 약속한 포맷팅용 문자열이 오고, 이후는 이 포맷팅용 문자열내에서 *place holder*로 사용된 부분을 대체할 실제 인수가 온다. 포맷팅 문자열 파싱후 어떤 타입의 인수가 몇개가 와야 하는지 파악해 가변 인수를 처리하는 식일 것이다.

### Syntax Notes

C에서는 `add_nums`, `printf` 예제 함수 원형에서 볼 수 있는 것처럼 첫번째 매개변수는 이름이 있어야 하고 이후에 가변 인수 지정 표기가 올 수 있다. C++의 경우는 이런 제약이 없다. 다음과 같은 함수를 선언하는 것이 가능하다:

```c++
int printz(...);
```

이런 식의 코드는 함수 구현에서 몇개의 인자가 오는지 알 수 없기 때문에 제대로 인자를 처리할 수가 없다. 함수 구현자와 호출자가 어떤 타입의 몇개 인자를 보내고 받을지 사전에 정하고 사용하면 되겠으나, 이렇게 하면 가변 인자를 사용하는 의미가 없다.

C++에서는 이런 식의 함수 정의를 **SFINAE**와 관련해 **메타프로그래밍**에서 사용하기도 한다. *overload resolution rule*상에서 `...`의 매치 우선 순위가 가장 낮다는 점을 활용한다. 이해와 편의를 위해 역시 cppreference 사이트의 예제 코드를 붙였다.

```c++
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

```c++
struct S
{ };

static_assert(is_class<S>::value);
static_assert(!is_class<int>::value);
```

이런 식의 메타프로그래밍 이외에 어떤 오버로딩된 함수 집합의 *fallback* 작업을 정의하기 위한 용도로도 사용할 수 있을 것이다.

추가로 C++에서는 다음 두 문법이 모두 허용된다. 둘다 동일한 의미다:

```c++
int f(int n, ...);
int g(int n...);
```

C++ 역사적으로 콤마를 사용하지 않는 방법이 먼저 도입된 후에 콤마가 붙은 C 스타일 문법이 추가되었다고 한다. 코드 호환성을 위해 두 문법을 다 유지하기로 결정했다고 한다.

> 좀더 자세한 사항은 다음을 참고하라: <http://en.cppreference.com/w/cpp/language/variadic_arguments>

### Can't be used with `__stdcall` calling convention

`__stdcall` 함수 호출 규약의 함수는 함수쪽에서 스택 클린업을 수행하게 코드가 생성되기 때문에 가변 인수를 사용할 수 없다. 컴파일러가 함수 자체에 대한 코드 생성시에 인자가 몇개 있는지 컴파일 타임에 계산할 수가 없기 때문에 스택 클린업 코드를 생성할 수 없다.

---

## Variadic Template, Parameter Pack and Parameter Pack Expansion

C++11부터는 템플릿과 관련해 새로운 문법이 추가되어 *n개*의 인수를 다룰 수 있게 되었다. 앞서 설명한 가변 인수와 구분해서 기억해야할 사항은 여기서 의미하는 n개라는 수는 특정 템플릿이 인스턴스화되는 컴파일 타임에 정해진다는 것이다. 즉 *런타임*에 처리하는 인수가 가변이 아니라 *컴파일 타임*에 템플릿에 넘기는 인자가 가변일 수 있다.

`printf`를 예로 들어 설명하면 `printf`를 호출하는 클라이언트 코드 자체에서는 `printf`에 넘기는 인자의 수를 컴파일 타임에 알 수 있다. 하지만 가변 인수 문법을 사용해 구현된 `printf` 자체에서는 이런 컴파일 타임 정보를 이용하지 않고 *런타임*에서 포맷팅 문자열을 파싱해 전달되는 인자의 수를 *계산*할 것이다.

여기서는 n개의 주어진 정수를 합해서 리턴해주는 간단한 코드를 예로 들어 여러 템플릿 피쳐 관련 코드에서 `...` 표기 사용을 설명하겠다.

> 설명하고자 하는 것이 템플릿 자체가 아니라 `...`을 어디에서 볼 수 있는 것인지에 대한 것이라서 가능하면 타입 적합성 체크나 타입 계산 코드는 예제에서 배제했다.

### function template

```c++
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

여기서 `typename... T` 부분을 **Parameter Pack**이라고 한다. parameter pack에 담긴 정보를 사용하기 위해 *풀어헤치는 작업*을 **Parameter Pack Expansion**이라고 한다. 예제에서 `T...`과 `n...`부분이 parameter pack expansion 표현이다. `T...`의 경우는 parameter pack으로 부터 *type*정보를 꺼내오는 것이고, `n...`은 대응되는 *value*값을 꺼내오는 것이다. 이렇게 parameter pack을 사용하는 어떤 템플릿을 **Variadic Template**이라고 한다.

이 `sum` 함수의 경우 다음과 같이 C++17 **fold expression** 문법을 적용하면 구현이 매우 단순해 진다:

```c++
template <typename... T>
constexpr auto sum(T... n)
{
    return (... + n);
}
```

이 버전의 구현에서 `(... + n)`과 같은 부분을 fold expression이라고 부른다. 보다시피 여기서도 `...`을 볼 수 있다.

> * **fold**는 *Functional Programming*에서 이야기하는 바로 그것이다. fold 연산 자체에 대한 설명은 여기서는 하지 않는다. 검색을 하거나 다른 포스트 [Fold it the way you like](https://ghjang.github.io/computer%20programming/2015/12/12/about-fold.html)를 참고하라.
> * parameter pack을 다루는 상황에서 fold expression을 사용할 수 없거나, 보통의 `...`으로 확장을 할 수도 없는 어떤 경우에 이용 가능한 일반적인 n개 인수 처리 방법은 인자 1개씩 끝어서 반복 처리해주는 **재귀호출**이다. 이는 템플릿 인스턴스화수의 증가등의 이유로 컴파일에 소요되는 시간에 좋지 못하다고 알려져 있다. 가능하면 최대한 재귀호출을 이용 가능한 다른 언어 피쳐로 대체해주는 것이 좋다. 

템플릿의 인수에 넘길 수 있는 *non-type template argument* 중에는 *정수 상수*가 있기 때문에 (그다지 유용하지 않겠지만) 다음처럼 `sum`을 구현할 수도 있다:

```c++
template <int... n>
constexpr auto sum()
{
    return (... + n);
}

static_assert(sum<1, 2, 3, 4, 5>() == 15);
```

이 변경된 예제에서 `int... n` 부분이 parameter pack이다. 보다시피 이미 정해진 `int`같은 타입으로도 pack을 표현할 수 있다. 이 경우 *타입* 정보는 이미 알고 있으니 풀어헤칠 필요가 없고 실제로 풀어헤칠 수도 없다. *값* 부분만 확장해서 사용할 수 있다.

당연한 것으로 여기서는 예제를 보이지 않으나 *멤버 함수 템플릿*에도 동일 내용이 적용된다.

### class template

함수 템플릿으로 구현한 `sum` 예제를 클래스 템플릿을 사용해 *수치 메타함수* 예제로 바꾸어 보겠다. 역시 `...` 표기에 대한 설명용이지 코드 자체에 그다지 유용한 의미가 있지는 않다.

```c++
template <int... n>
struct sum
{
    constexpr static auto value = (... + n);
};

static_assert(sum<1, 2, 3, 4, 5>::value == 15);
```

`int... n` parameter pack의 타입 정보를 `int`로 고정하지 않고 다음처럼 단순히 컴파일 타임에 결정되는 *type*을 받게 변경하면 컴파일 에러가 발생한다.

```c++
template <typename... n>
struct sum
{
    constexpr static auto value = (... + n);
};
```

여기서 `n`은 타입 자체이기에 `+` 연산을 할 수가 없다. 이런 수치계산 문제를 해결하는 *idiom*이 있는데 다음과 같은 식이다:

```c++
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

(*타입 계산*이라는 메타프로그래밍적 개념에 익숙하지 않다면 '*이게 뭔 뻘짓인가?*'라고 생각할지도 모르겠다.)

이 변경된 예제에서 이전에서 보지 못했던 부분은 `std::integral_constant<T, n>...`이다. 별다른 것은 아니고 `T... n` parameter pack을 확장한 표현식이다. (fold expression은 좀 유별나긴 하지만,) 이처럼 `...` 확장 표기가 `T`나 `n`에 곧바로 붙어 있을 필요는 없다.

### using type alias

바로 전의 `int` 타입의 *정수값를 타입으로 랩핑*한 것을 받는 `sum` 클래스 템플릿 예제는 그 사용법이 매우 번잡하다. 이런 식의 클래스 템플릿이 주어졌을 때 코드 사용자 측에서 간편하게 사용할 수 있게 다음과 같은 추가적인 타입 별칭을 만들어 줄 수 있다. 보다시피 `using` 타입 별칭에서도 n개 인자를 지정하는 템플릿이 사용가능하다:

```c++
template <typename T, T... n>
using sum_t = sum<std::integral_constant<T, n>...>;

static_assert(sum_t<int, 1, 2, 3, 4, 5>::value == 15);
```

### variable template & inline variable

*C++14*에서는 변수조차 템플릿화가 가능해졌다. *C++17*에서는 변수를 인라인화할 수도 있다. 이 두가지 피쳐를 이용하면 바로 전의 `sum_t` 코드를 다음과 같이 고칠 수 있다. 애당초 원했던 것은 합산된 *값*이지 *타입*이 아니지 않은가?:

```c++
template <typename T, T... n>
inline constexpr auto sum_v = sum<std::integral_constant<T, n>...>::value;

static_assert(sum_v<int, 1, 2, 3, 4, 5> == 15);
```

여기서 `sum_v<int, 1, 2, 3, 4, 5>`이라는 표현 자체는 *인라인화된 변수*, 여기서는 그냥 *상수값*이 된다. 이 표현은 그닥 변수라는 느낌이 들지 않는다. 해서 변수임을 극대화해서 나타내기 위해 배열을 템플릿화해서 다음처럼 표현해봤다:

```c++
#include <iterator> // for std::size

// ...

template <typename T, T... n>
constexpr static T g_table[] = {
    sum_v<T, n...>,
    sum_v<T, (n + 1)...>,
    sum_v<T, (n * n)...>
};

static_assert(std::size(g_table<int, 1, 2, 3, 4, 5>) == 3);
static_assert(g_table<int, 1, 2, 3, 4, 5>[0] == 15);
static_assert(g_table<int, 1, 2, 3, 4, 5>[1] == 20);
static_assert(g_table<int, 1, 2, 3, 4, 5>[2] == 55);
```

`std::size`는 C++17에 추가된 것으로 인자로 배열을 넘길 경우 크기를 리턴해 준다.

또한 변수 템플릿은 *특수화*가 가능하다. `g_table`을 다음과 같이 특수화해줄 수도 있다:

```c++
#include <utility> // for std::integer_sequence

// ...

template <typename T, T... n>
constexpr static T g_table<std::integer_sequence<T, n...>>[sizeof...(n)][2] = {
    { n, sum_v<T, n, n + 1, n * n> }...
};

using int_seq_t = std::make_index_sequence<5>;
static_assert(g_table<int_seq_t>[0][0] == 0);
static_assert(g_table<int_seq_t>[0][1] == 1);
static_assert(g_table<int_seq_t>[1][0] == 1);
static_assert(g_table<int_seq_t>[1][1] == 4);
static_assert(g_table<int_seq_t>[2][0] == 2);
static_assert(g_table<int_seq_t>[2][1] == 9);
static_assert(g_table<int_seq_t>[3][0] == 3);
static_assert(g_table<int_seq_t>[3][1] == 16);
static_assert(g_table<int_seq_t>[4][0] == 4);
static_assert(g_table<int_seq_t>[4][1] == 25);
```

`std::integer_sequence`에 대해서 `g_table` 변수 템플릿을 *부분 특수화*한 것으로 보다시피 심지어 배열을 1차원에서 2차원으로 바꾸는 것도 가능하다. (이 코드 자체는 굉장히 쓸모없어 보이긴 한다. 이런 다양한 표현을 하는 것이 가능하다는 느낌만 받으면 될 것 같다.)

코드에 보이는 `sizeof...(n)`이라는 표현은 parameter pack의 크기를 얻어오는 문법이다. 여기서도 `...`을 볼 수 있다.

### generic lambda

C++에서 *로컬 컨텍스트*, 그러니까 함수 코드 블럭 안쪽 등에서 템플릿을 선언할 수 없다. 다음과 같은 코드 작성이 불가하다는 것이다:

```c++
void func()
{
    template <typename T>
    struct X
    {
        // ... 
    };
}
```

다음도 컴파일 에러다:

```c++
void func()
{
    struct X
    {
        template <typename T>
        void run()
        {
            // ...
        } 
    };
}
```

C++14에는 **generic lambda**라는 람다 표현식이 추가하여 제한적이지만 로컬 영역에서 템플릿을 정의해 사용하는 것과 같은 효과를 낼 수 있다. 다음과 같은 모양의 코드다:

```c++
void func()
{
    constexpr auto val = [](auto... n) { return (... + n); }(1, 2, 3, 4, 5);
    static_assert(val == 15);
}
```

이 코드는 대략 다음 코드에 대한 *syntax sugar*이다:

```c++
struct F
{
    template <typename... T>
    constexpr auto operator () (T... n) const
    { return (... + n); }
};

void func()
{
    constexpr auto val = F{}(1, 2, 3, 4, 5);
    static_assert(val == 15);
}
```

예제에서 보다시피 제네릭 람다 표현식의 함수 인자 목록에 `auto`와 `...`을 지정한 것을 볼 수 있다. 일반 함수의 경우는 이런 식으로 인수 목록에 `auto`를 사용할 수 없고 물론 `...`도 지정할 수 없다. 매우 예외적인 케이스다.

> 여기서 설명하지는 않았지만 보통의 템플릿 정의 구문과 유사한 표현을 포함하는 또다른 람다 표현식 형태가 표준에 추가된 것 같다. (제네릭 람다 형태로는 람다 표현식에 넘어가는 타입 제어를 표현하기 쉽지가 않아서 그런 것 같다.)

### etc...

(여기까지만 보아도 이 `...` 표기에 익숙하지 않은 이는 혼란 스러워 할지도 모르겠다.) 다음과 같은 표현도 가능하다.

```c++
template <template <typename...> class... T>
struct F
{
    // ...
};
```

여기서 `F`는 *n개의 타입인자를 받는 클래스 템플릿 m개를 받는 클래스 템플릿*이다.

이 `T`처럼 *타입이 아닌 템플릿* 자체를 받는 non-type 파라미터를 **template template parameter**라고 한다.

이 것을 활용하는 방법은 코드 작성자의 상상과 능력에 달려있지 않을까 싶기도 하다. *고차 메타함수*를 작성하는데 사용할 수도 있을 것 같고, *policy-based* 클래스 디자인에도 사용할 수 있을 것 같다. (템플릿-템플릿 파라미터를 n개 다루는 경우가 그리 유용할지는 잘 모르겠다.) 대략 아래와 같은 식으로 말이다:

```c++
template <typename T>
struct Impl0
{
    // ...
};

template <typename T, typename U = int>
struct Impl1
{
    // ...
};

template <typename... T>
struct Impl2
{
    // ...
};


template
<
    typename T,
    template <typename...> class... Policy
>
struct Product : Policy<T>...
{
    // ...
};


struct S
{ };

Product<S, Impl0, Impl1, Impl2> p;
// ...
```

`struct Product : Policy<T>...` 표현과 같이 *상속* 컨텍스트에서도 이런 식으로 `...`을 사용할 수도 있다.

---

## Variadic Template + Variadic Arguments

좀 특이한 경우인데 `.`이 6개(`......`)가 있는 코드가 정상적으로 컴파일되는 컨텍스트가 있다. 예를 들면 다음과 같은 코드가 정상적으로 동작한다:

```c++
template <typename T>
struct is_variadic_function : std::false_type
{ };

template <typename R, typename... Args>
struct is_variadic_function<R(Args......)> : std::true_type
{ };

static_assert(std::is_function_v<decltype(std::printf)>);
static_assert(std::is_function_v<decltype(std::atoi)>);

static_assert(is_variadic_function<decltype(std::printf)>());
static_assert(!is_variadic_function<decltype(std::atoi)>());
```

여기서 `is_variadic_function` 메타함수는 주어진 타입이 *가변 인수*를 가지는 *함수 타입*인지를 판별해준다. 본 글 앞쪽의 C++ 가변 인수 문법 설명부분을 참조하라. 이 메타함수의 부분 특수화 코드를 다음과 같이 작성해주는 것이 좀더 의도가 명확할 것 같긴 하다:

```c++
template <typename R, typename... Args>
struct is_variadic_function<R(Args..., ...)> : std::true_type
{ };
```

(이런 *타입 트레잇* 부류의 메타함수가 아닌 일반 함수에서 이 두가지 의미의 `...`을 혼합해서 사용하는 것은 딱히 의미 없을 것 같다(?). 앞쪽의 템플릿 가변 인수 부분에 전부 매칭되고 마지막 일반 가변 인수에는 0개 인자를 넘기게 처리되지 않겠는가?)

---

## Variadic Macro

마지막으로 템플릿은 아니지만 알아두면 간혹 유용하게 사용가능한 C++11 **Variadic Macro**에 대해 간략히 적는다. 다른게 아니라 *function-like macro*에 `...` 표기로 n개 인자를 지정하는 것이 가능하다. cppreference에서 볼 수 있는 예제 코드를 수정해 붙였다:

```c++
#define showlist(...) puts(#__VA_ARGS__)
#define showlist_with_prefix(prefix, ...) puts(#prefix "_" #__VA_ARGS__)

void f()
{
    showlist();            // expands to puts("")
    showlist(1, "x", int); // expands to puts("1, \"x\", int")

    showlist_with_prefix(XXX);              // expands to puts("XXX_")
    showlist_with_prefix(XXX, 1, "X", int); // expands to puts("XXX_1, \"x\", int")
}
```

여기서 `showlist` 매크로 함수의 인자 목록에 있는 `...`을 parameter pack에 대응되는 개념이라고 생각하면 된다. 매크로 함수의 parameter pack 확장은 필요한 곳에 `__VA_ARGS__`를 넣어주면 된다. 당연한 이야기지만 이러한 처리는 컴파일러가 해주는게 아니라 *전처리기*가 컴파일 작업전에 해준다.

---

## Conclusion

`...` 표기를 통한 컴파일 타임 가변 템플릿 인수를 다루는 표현의 도입으로 C++11 이전에는 매우 불편하고 비효율적이며 지저분한 코드가 단순해졌다. 코드의 표현력 또한 향상시켰다. 여기에서 다루지 않은 `...` 표기 사용 컨텍스트도 있겠다(예: *예외 핸들러*). 또한 다음 표준에서도 계속 언어 스펙이 확장되기에 좀더 사용 범위가 늘어날 듯하다.

parameter pack과 관련된 좀더 자세한 사항은 다음을 참고하라:

* <http://en.cppreference.com/w/cpp/language/parameter_pack>
