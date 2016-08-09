---
layout: post
title: "tuple_cat in a Dumb Way"
description: ""
category: Computer Programming
tags: [C++]
---
{% include JB/setup %}

n개의 `tuple` 객체들을 하나의 `tuple` 객체로 합치는 [tuple_cat](http://en.cppreference.com/w/cpp/utility/tuple/tuple_cat) 함수를 직접 작성해보도록 한다. 여기서는 효율성등을 고려하지 않고 기본적인 C++ template 사용법만을 가지고 구현을 시도했다.

다소 복잡한 C++ TMP를 이용해서 효율적인 `tuple_cat`을 구현하는 것과 관련하여 아래의 글들을 참고할만하다:

+ [Tiny Metaprogramming Library](http://ericniebler.com/2014/11/13/tiny-metaprogramming-library/)
+ [Simple C++11 metaprogramming](http://pdimov.com/cpp2/simple_cxx11_metaprogramming.html)

---

## Take-1: Ordinary Recursion Way

n개의 인자에 대해서 재귀함수호출을 통하여 인자들을 하나씩 끊어서 처리하는 아래와 같은 일반적인 구현을 우선 생각해볼 수 있겠다.

```cpp
namespace Detail
{
    template
    <
            typename T, std::size_t... i,
            typename U, std::size_t... j
    >
    auto binary_tuple_cat(T && a, std::index_sequence<i...>,
                          U && b, std::index_sequence<j...>)
    {
        return std::make_tuple(
                std::get<i>(std::forward<T>(a))...,
                std::get<j>(std::forward<U>(b))...
        );
    }
} // namespace Detail

//...

auto tuple_cat_()
{
    return std::make_tuple();
}

template <typename T>
auto tuple_cat_(T && a)
{
    return a;
}

template <typename T, typename U>
auto tuple_cat_(T && a, U && b)
{
    constexpr auto aSize = std::tuple_size<std::decay_t<T>>::value;
    constexpr auto bSize = std::tuple_size<std::decay_t<U>>::value;
    return Detail::binary_tuple_cat(
            std::forward<T>(a),
            std::make_index_sequence<aSize>{},
            std::forward<U>(b),
            std::make_index_sequence<bSize>{}
    );
}

template <typename T, typename... R>
auto tuple_cat_(T && a, R &&... rs)
{
    return tuple_cat_(
            std::forward<T>(a),
            tuple_cat_(std::forward<R>(rs)...)
    );
}
```

---

## Take-2: Applying Fold-Right High-Order Function

'주어진 시퀀스의 값들을 이항함수를 이용해서 합친다.'

조금만 생각해보면 위의 `tuple_cat_` 구현은 전형적인 fold-right 알고리즘 패턴과 닮아 있음을 알 수가 있다.

먼저 재귀함수호출 형태의 `fold_right` high-order function을 구현하면 아래와 같을 수 있겠다.

```cpp
template <typename F, typename T, typename U>
auto fold_right(F && f, T && init, U && last)
{
    return f(std::forward<U>(last), std::forward<T>(init));
}

template <typename F, typename T, typename U, typename... R>
auto fold_right(F && f, T && init, U && first, R &&... rs)
{
    return f(
            std::forward<U>(first),
            fold_right(
                    std::forward<F>(f),
                    std::forward<T>(init),
                    std::forward<R>(rs)...
            )
    );
}
```

다음으로 `fold_right`에 넘길 이항함수 코드를 작성해준다. 이 이항함수는 두개의 `tuple`객체들을 받아서 하나의 `tuple`객체로 합쳐주는 역할을 해주면 된다. 여기서는 `BinaryTupleCat struct`를 정의하고 함수연산자를 오버로딩해서 구현하도록했다. 앞서 먼저 구현한 `binary_tuple_cat` 함수를 재사용할 수 있겠다.

```cpp
namespace Detail
{
    // ...

    struct BinaryTupleCat
    {
        template <typename T, typename U>
        auto operator () (T && a, U && b) const
        {
            constexpr auto aSize = std::tuple_size<std::decay_t<T>>::value;
            constexpr auto bSize = std::tuple_size<std::decay_t<U>>::value;
            return binary_tuple_cat(
                    std::forward<T>(a),
                    std::make_index_sequence<aSize>{},
                    std::forward<U>(b),
                    std::make_index_sequence<bSize>{}
            );
        }
    };
} // namespace Detail
```

마지막으로 `tuple_cat_` 함수를 `fold_right` 함수를 이용해서 다시 구현하면 아래와 같다.

```cpp
template <typename... T>
auto tuple_cat_(T &&... ts)
{
    return fold_right(
            Detail::BinaryTupleCat{},
            std::make_tuple(), // empty tuple for init.
            std::forward<T>(ts)...
    );
}
```

---

## Take-3: Trying to Use C++17 Built-in Fold Feature

C++17부터는 언어문법 자체에 fold적인 작업에 대한 지원이 추가되었다.

NOTE: not written yet.

---

## 참고

+ <https://github.com/ghjang/personal_study/blob/master/cpp/tuple_cat/main.cpp>
