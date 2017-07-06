---
title: "E.T. What Are You?"
description: ""
category: Computer Programming
tags: [C++, C++ TMP, C++ Expression Template]
---



---

## Motivation



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
