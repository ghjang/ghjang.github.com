---
title: "E.T. What Are You?"
description: ""
category: Computer Programming
tags: [C++, C++ TMP, C++ Expression Template]
---

C++의 Expression Template 이야기를 좀 풀어봤다. Expression Template이라고 매번 길게 타이핑하는 노가다를 피하고자 이후에는 ET라고 줄여 쓰겠다.

> E.T.(Extra-Terrestrial)가 아님에 주의할 것.

ET의 원류는 행렬 계산 문제 코드의 최적화와 관련이 있는다는 것이 널리 알려져 있다. 이 기법은 비슷한 시기에 서로 다른 두 분이 각각 고안했다고 한다. 코드 표현의 추상적인 측면을 유지하면서도 고성능의 코드를 생성해낸다는 기법이다.

여기서는 최적화된 코드를 생성하는 측면에서의 ET가 아니라 표현력 확장이라는 측면에서의 ET를 설명하겠다. 대략 ET가 어떻게 구현된다는 것인지를 개념이라도 파악한다면 좀더 어려운 문제인 최적화 코드 생성에 어떻게 써먹을 수 있는지 생각해 볼 수 있지 않을까 싶다. 최적화 문제는 컴파일러가 코드를 생성하는 방식, 다루는 문제의 핵심사항, 적용 환경등에 대해 잘 파악하고 있어야 풀 수 있는 문제다. ET 기법을 쓴다고 공짜로 최적화가 된다는 것이 아니고, 그런 최적화된 코드를 생성하는 데 훌륭한 도구가 될 수 있겠다.

참고로 최초의 ET 기법 소개 글은 다음에서 찾아볼 수 있다. 이외에도 검색을 해보면 많은 자료들을 찾아볼 수 있다:

+ [Expression Templates - Todd Veldhuizen](https://web.archive.org/web/20050210090012/http://osl.iu.edu/~tveldhui/papers/Expression-Templates/exprtmpl.html): 아카이빙된 웹문서. 저자는 Todd Veldhuizen이다. 아래에서 언급하는 책보다 보통 이 글을 ET관련 글들에서 더 언급하는 것 같다.
+ [C++ Templates - The Complete Guide](http://www.josuttis.com/tmplbook/tmplbook.html): 종이책이다. 저자 중에 한명인 David Vandevoorde이 ET 기법을 고안했었다고 한다. 책에 ET를 설명하는 부분이 있다.

---

## Motivation

### Lambda Expression

여기서 말하는 람다 표현식은 C++11에 추가된 언어 자체의 람다 표현식이 아니고 ET로 구현한 람다 표현식을 의미한다. 아래와 같은 코드를 작성해 보자는 것이다:

```cpp
```

### Catch.hpp's Test Expression Printing

C++ Catch 테스트 프레임워크를 사용해보면 테스트 실패시에 테스트 실패를 일으킨 표현식을 콘솔에 출력해 준다:

```cpp
```

어떻게 C++ 표현식 자체를 콘솔에 찍을 수 있겠는가? 뭐 예상대로 해답은 ET다.

---

## Operator Overloadings for Capturing C++ Expressions

```cpp
    template <typename Left, typename OpTag, typename Right>
    struct non_terminal
    { };
```

```cpp
    namespace detail
    {
        template <typename T>
        struct is_non_terminal_impl : std::false_type
        { };

        template <typename Left, typename OpTag, typename Right>
        struct is_non_terminal_impl<non_terminal<Left, OpTag, Right>>
                : std::true_type
        { };
    } // namespace detail

    template <typename T>
    struct is_non_terminal
            : detail::is_non_terminal_impl<std::decay_t<T>>
    { };

    template <typename T>
    inline constexpr auto is_non_terminal_v = is_non_terminal<T>::value;
```

```cpp
    struct place_holder
    { };

    using place_holder_t = non_terminal<place_holder, void, place_holder>;

    inline constexpr place_holder_t _1{};
```

```cpp
    struct equal_to_t
    { };
```

```cpp
    template
    <
        typename Lhs,
        typename Left, typename OpTag, typename Right
    >
    auto operator == (Lhs && lhs,
                      non_terminal<Left, OpTag, Right> & rhs)
    {
        using lhs_t = std::decay_t<Lhs>;
        using rhs_t = non_terminal<Left, OpTag, Right>;
        return non_terminal<lhs_t, equal_to_t, rhs_t>{};
    }

    template
    <
        typename Lhs,
        typename Left, typename OpTag, typename Right
    >
    auto operator == (Lhs && lhs,
                      non_terminal<Left, OpTag, Right> const& rhs)
    {
        using lhs_t = std::decay_t<Lhs>;
        using rhs_t = non_terminal<Left, OpTag, Right>;
        return non_terminal<lhs_t, equal_to_t, rhs_t>{};
    }

    template
    <
        typename Lhs,
        typename Left, typename OpTag, typename Right
    >
    auto operator == (Lhs && lhs,
                      non_terminal<Left, OpTag, Right> && rhs)
    {
        using lhs_t = std::decay_t<Lhs>;
        using rhs_t = non_terminal<Left, OpTag, Right>;
        return non_terminal<lhs_t, equal_to_t, rhs_t>{};
    }
```

```cpp
    template
    <
        typename Left, typename OpTag, typename Right,
        typename Rhs,
        typename = std::enable_if_t<!is_non_terminal_v<Rhs>>
    >
    auto operator == (non_terminal<Left, OpTag, Right> & lhs,
                      Rhs && rhs)
    {
        using lhs_t = non_terminal<Left, OpTag, Right>;
        using rhs_t = std::decay_t<Rhs>;
        return non_terminal<lhs_t, equal_to_t, rhs_t>{};
    }

    template
    <
        typename Left, typename OpTag, typename Right,
        typename Rhs,
        typename = std::enable_if_t<!is_non_terminal_v<Rhs>>
    >
    auto operator == (non_terminal<Left, OpTag, Right> const& lhs,
                      Rhs && rhs)
    {
        using lhs_t = non_terminal<Left, OpTag, Right>;
        using rhs_t = std::decay_t<Rhs>;
        return non_terminal<lhs_t, equal_to_t, rhs_t>{};
    }

    template
    <
        typename Left, typename OpTag, typename Right,
        typename Rhs,
        typename = std::enable_if_t<!is_non_terminal_v<Rhs>>
    >
    auto operator == (non_terminal<Left, OpTag, Right> && lhs,
                      Rhs && rhs)
    {
        using lhs_t = non_terminal<Left, OpTag, Right>;
        using rhs_t = std::decay_t<Rhs>;
        return non_terminal<lhs_t, equal_to_t, rhs_t>{};
    }
```

```cpp
TEST_CASE("equal_to expressions", "[expresso]")
{
    auto expr0 = 1 == 1;
    static_assert(std::is_same<bool, decltype(expr0)>());

    auto expr1 = _1 == 1;
    auto expr2 = 1 == _1;
    auto expr3 = _1 == _1;
    static_assert(is_non_terminal_v<decltype(expr1)>);
    static_assert(is_non_terminal_v<decltype(expr2)>);
    static_assert(is_non_terminal_v<decltype(expr3)>);

    auto expr4 = _1 == 1 == 2;
    auto expr5 = 1 == (_1 == 2);
    static_assert(is_non_terminal_v<decltype(expr4)>);
    static_assert(is_non_terminal_v<decltype(expr5)>);

    // both sides are lvalue non_terminal.
    auto expr6 = expr4 == expr5;
    static_assert(is_non_terminal_v<decltype(expr6)>);

    auto const expr7 = expr6 == _1;
    static_assert(is_non_terminal_v<decltype(expr7)>);
}
```

---

## Adding More Operator Overloadings

### Binary Operators

### Unary Operators

---

## Printing the Compile-time Expression Type Tree

---

## References

+ 
