---
title: "dot-dot-dot is everywhere."
description: ""
category: Computer Programming
tags: [C++, C++ TMP]
---

C++에 관심이 좀 있다면 "`...`" 표기를 C++11부터 도처에서 볼 수 있다. 관련 내용을 설명하는 좋은 자료들도 찾아보면 많다. 여기서는 머리 속 정리도 할 겸 "`...`" 표기가 얼마나 많이 C++에서 사용되고 있는지 간략히 나름대로 정리해 본다.

---

## Variadic Arguments

C를 배워본 사람이라면 `Hello World!`를 `printf`로 출력해 보았을 것이다. 함수 원형은 다음과 같다.

```c
int printf( const char* format, ... );
```

여기서 마지막 인수 부분 "`...`"은 **가변 인수**다. 즉 *0개 이상의 인자*를 지정할 수 있다. 아래와 같은 호출이 가능하다.

```c
printf("Hello World!");
printf("Hello %s World!", "Wonderful");
printf("Hello %s World%c", "Wonderful", '!');
printf("Hello %d %s World%c", 2, "Wonderful", '!');
```

가변 인수를 처리하는 함수 구현 예제는 편의상 cppreference 사이트에서 가져와 붙였다.

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

`printf` 함수에 대해 잠시 생각해보면 첫번째 인수는 약속한 포맷팅용 문자열이 오고, 이후는 이 포맷팅용 문자열내에서 *place holder*로 사용된 부분을 대체할 실제 인수가 온다. 포맷팅 문자열 파싱후 어떤 타입의 인수가 몇개가 와야 하는지 파악해 가변 인수를 처리하는 식일 것이다.

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

이런 식의 메타프로그래밍 이외에 어떤 작업의 *fallback* 작업을 정의하기 위한 용도로도 사용할 수 있을 것이다.

추가로 C++에서는 다음 두 문법이 모두 허용 한다. 둘다 동일한 의미다:

```cpp
int f(int n, ...);
int g(int n...);
```

C++ 역사적으로 콤마를 사용하지 않는 방법이 먼저 도입된 후에 콤마가 붙은 C 스타일 문법이 추가되었다고 한다. 코드 호환성을 위해 두 문법을 다 유지하기로 결정했다고 한다.

> 좀더 자세한 사항은 다음을 참고하라: <http://en.cppreference.com/w/cpp/language/variadic_arguments>

### Can't be used with `__stdcall` calling convention

`__stdcall` 함수 호출 규약의 함수는 함수쪽에서 스택 클린업을 수행하게 코드가 생성되기 때문에 가변 인수를 사용할 수 없다. 컴파일러가 코드 생성시에 인자가 몇개 있는지 컴파일 타임에 계산할 수가 없기 때문에 스택 클린업 코드를 생성할 수 없다.

---

## Parameter Pack

> 좀더 자세한 사항은 다음을 참고하라: <http://en.cppreference.com/w/cpp/language/parameter_pack>

---

## Variadic Template

---

## Variadic Macro

