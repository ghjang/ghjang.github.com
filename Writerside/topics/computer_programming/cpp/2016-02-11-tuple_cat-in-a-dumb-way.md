---
title: "tuple_cat in a Dumb Way"
description: ""
category: Computer Programming
tags: [C++]
---

n개의 `std::tuple` 객체들을 하나의 `std::tuple` 객체로 합치는 [std::tuple_cat](http://en.cppreference.com/w/cpp/utility/tuple/tuple_cat) 함수를 직접 작성하는 **연습**을 해보도록 하겠다. 여기서는 효율성등을 고려하지 않고 기본적인 **C++ template** 사용법만을 가지고 구현을 시도한다.

다소 복잡한 C++ TMP를 이용해서 효율적인 `tuple_cat`을 구현하는 것과 관해서는 아래의 글들을 참고하라:

+ [Tiny Metaprogramming Library](http://ericniebler.com/2014/11/13/tiny-metaprogramming-library/)
+ [Simple C++11 metaprogramming](http://pdimov.com/cpp2/simple_cxx11_metaprogramming.html)

---

## Take-1: Ordinary Recursion Way

n개의 인자에 대해서 재귀함수호출로 인자를 하나씩 끊어서 처리하는 아래와 같은 일반적인 구현을 우선 생각해볼 수 있다.

```c++
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

**_'주어진 시퀀스의 값들을 이항함수를 이용해서 합친다.'_**

조금만 생각해보면 위의 `tuple_cat_` 구현은 전형적인 **fold 알고리즘** 패턴과 닮아 있음을 알 수가 있다.

먼저 재귀함수호출 형태의 `fold_right` 고차함수를 구현하면 아래와 같을 수 있다.

```c++
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

다음으로 `fold_right`에 넘길 이항함수 코드를 작성해준다. 이 이항함수는 두개의 `tuple`객체들을 받아서 하나의 `tuple`객체로 합쳐주는 역할을 해주면 된다. 여기서는 `BinaryTupleCat struct`를 정의하고 함수연산자를 오버로딩해서 구현하도록했다. 앞서 먼저 구현한 `binary_tuple_cat` 함수를 재사용할 수 있다.

```c++
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

```c++
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

별다른 생각없이 C++17 fold 표현식을 사용하여 먼저 작성해본 `fold_right` 함수에 해당하는 기능을 대체 시도해보았다. 하지만 우선 결과는...

```c++
template <typename T, typename U>
auto operator + (T && a, U && b)
{
    return Detail::BinaryTupleCat{}(
            std::forward<T>(a),
            std::forward<U>(b)
    );
}

auto tuple_cat_()
{
    return std::make_tuple();
}

template <typename... T>
auto tuple_cat_(T &&... ts)
{
    return (... + std::forward<T>(ts)) // fold-right
}
```

**_컴파일에러!!_**

```c++
/Users/gilhojang/GitHub/personal_study/cpp/tuple_cat/main.cpp:138:19: error: call to function 'operator+' that is neither visible in the template definition nor found by argument-dependent lookup
/Users/gilhojang/GitHub/personal_study/cpp/tuple_cat/main.cpp:160:14: note: in instantiation of function template specialization 'tuple_cat_<std::__1::tuple<int, double, char> &, std::__1::tuple<const char *, float, double> &>' requested here
    auto t = tuple_cat_(a, b);
             ^
/Users/gilhojang/GitHub/personal_study/cpp/tuple_cat/main.cpp:120:6: note: 'operator+' should be declared prior to the call site
auto operator + (T && a, U && b)
     ^
```

`tuple_cat_` 템플릿함수에서 `std::tuple` 객체에 대해 오버로딩한 `operator +` 함수를 C++17 fold 표현식내에서 찾을 수 없다는 내용이다. `operator +`를 `std::tuple` 선언전에 놓아야한다는 것 같지만 현재의 이 구조로는 불가능한 일이다. C++17 fold 표현식에서는 C++ 자체의 연산자를 이용한 표현식만을 사용할 수 있다. `std::tuple` 자체에 `tuple_cat` 용도의 연산자 오버로딩을 **새로이** 추가할 수는 없다. `std::tuple`에 대한 `operator +` 오버로딩이 원래 표준에 있었다면 **템플릿 특수화**의 형태로 추가가 가능은 했을 것이다. 이 것에 대한 우회책으로 `std::tuple` 인자를 wrapping해주는 방법을 생각해 볼 수 있다. 대략 다음과 같은 식이라는 것이다.

```c++
template <typename T, typename F>
struct ArgWrap
{
    template <typename U>
    auto operator + (ArgWrap<U, F> rhs) const
    {
        using result_t = decltype(F{}(m_, rhs.m_));
        return ArgWrap<result_t, F>{ F{}(m_, rhs.m_) };
    }

    T m_;
};

auto tuple_cat_()
{
    return std::make_tuple();
}

template <typename... T>
auto tuple_cat_(T &&... ts)
{
    // fold-right
    auto r = (
        ...
        + ArgWrap<
                std::decay_t<T>,
                Detail::BinaryTupleCat
            >{ std::forward<T>(ts) }
    );
    return std::move(r.m_);
}
```

동작을 하기는 하지만 언뜻봐도 문제점들이 곧 보인다:

1. 전형적으로 알고있던 fold부류의 함수의 모양새가 아니어서 처음에는 좀 불편할 수 있다.
2. **value category**에 대한 고려가 없어 성능개선이 필요하다. `tuple_cat_`에 **rvalue**인자만 넘어오면 문제없지만 **lvalue**인자가 있을 경우 복사가 발생한다.

1번 문제의 경우는 구현을 조금만 손보면 해결볼 수 있다.

```c++
template <typename F, typename... T>
auto fold_right_impl(T &&... xs)
{
    auto r = (... + ArgWrap<std::decay_t<T>, F>{ std::forward<T>(xs) });
    return std::move(r.m_);
}

template <typename F, typename T, typename... R>
auto fold_right(T && init, R &&... rs)
{
    return fold_right_impl<F>(
            std::forward<R>(rs)...,
            std::forward<T>(init)   // append to the right-most position.
    );
}

// ...

template <typename... T>
auto tuple_cat_(T &&... ts)
{
    return fold_right<Detail::BinaryTupleCat>(
            std::make_tuple(), // init for fold_right
            std::forward<T>(ts)...
    );
}
```

성능개선을 고려하지 않는다면 여기 정도에서 멈추는 것이 코드 모양새가 적당한듯하다.

허나 C++ 프로그래머라면 성능관련하여 일종의 강박증 같은 것이 있기 때문에 한번더 리팩터링에 들어가리라... :)

lvalue `std::tuple` 객체가 `tuple_cat_`에 넘겨질 경우 `ArgWrap`의 `m_` 멤버변수에 복사생성이 일어나게 된다. 이 문제를 해결하려면 lvalue 객체가 전달될 경우 `std::tuple<...> const&` 형태의 **타입**으로 멤버객체를 저장하면 될 것도 같다. **타입을 제어해야하는 상황**,... 그렇다 **C++ Template Metaprogramming** 기법을 도입해야한다는 것이다. 덕분에 구현이 좀 지저분해졌다. :( 변경된 코드부분은 아래와 같다.

```c++
template <typename T, typename F, bool isRvalueArg>
struct ArgWrap;

template <typename T, typename F>
struct ArgWrap<T, F, true>
{
    template <typename U>
    auto operator + (ArgWrap<U, F, true> && rhs) const
    {
        using result_t = decltype(F{}(std::move(m_), std::move(rhs.m_)));
        return ArgWrap<result_t, F, true>{ F{}(std::move(m_), std::move(rhs.m_)) };
    }

    template <typename U>
    auto operator + (ArgWrap<U, F, false> && rhs) const
    {
        using result_t = decltype(F{}(std::move(m_), rhs.m_));
        return ArgWrap<result_t, F, true>{ F{}(std::move(m_), rhs.m_) };
    }

    mutable T m_;
};

template <typename T, typename F>
struct ArgWrap<T, F, false>
{
    template <typename U>
    auto operator + (ArgWrap<U, F, true> && rhs) const
    {
        using result_t = decltype(F{}(m_, std::move(rhs.m_)));
        return ArgWrap<result_t, F, true>{ F{}(m_, std::move(rhs.m_)) };
    }

    template <typename U>
    auto operator + (ArgWrap<U, F, false> && rhs) const
    {
        using result_t = decltype(F{}(m_, rhs.m_));
        return ArgWrap<result_t, F, true>{ F{}(m_, rhs.m_) };
    }

    T const& m_;
};

template <typename F, typename... T>
auto fold_right_impl(T &&... xs)
{
    auto r = (
        ...
        + ArgWrap<
                std::decay_t<T>,
                F,
                std::is_rvalue_reference<decltype(xs)>::value
            >{ std::forward<T>(xs) }
    );
    return std::move(r.m_);
}
```

변경된 `ArgWrap`에서 `operator +`를 rvalue레퍼런스에 대해서만 오버로딩한 것은 `ArgWrap`은 세부구현사항이고 lvalue객체로 사용될 경우가 없기 때문이다.

`ArgWrap` 특수화내 일종의 **tag dispatch pattern** 형태의 오버로딩된 코드는 C++17에 추가된다는 `if constexpr` 같은 코어언어기능을 이용하면 좀더 간소화할 수 있겠다.

최종적인 코드를 테스트를 많이 한 것은 아니라 구현이 정확하지는 않을 수도 있다. **연습**을 목표로 한 것이니 이정도에서 만족하고 접도록 한다.

개인적으로 **fold적인 사고방식**을 가지려고 노력중이다. 재미진다. **Functional Programming**에서 fold를 가리켜서 **Super High-Order Function**이라고 하는 경우도 봤다. 그만큼 매우 빈번히 사용하고 강력하다는 것이다. 많은 알고리즘이 최종적으로 fold로 귀결되는 모습을 보고 있자면 가끔 경이롭기까지한 느낌이랄까나.

C++17 fold 관련하여 좀더 자세히 알고 싶다면 아래 '참고' 섹션에 있는 링크글들을 참조해보시라. ~ :)

---

## 참고

+ [Fun with folds](https://ngathanasiou.wordpress.com/2015/12/15/182/)
+ [Lazily evaluated folds in C++](https://ngathanasiou.wordpress.com/2016/03/22/lazily-evaluated-folds-in-c/): 'Fun with folds' 글을 쓴 저자의 C++17 fold 관련내용 제2탄. **Expression Template**관련 내용이 다루어진다. '어디까지 가는거냐?' :)
+ [Fold it the way you like]({{ site.baseurl }}{% post_url 2015-12-12-about-fold %}): C++17 fold에 관한 내용은 아님. C++ TMP 공부하는 와중에 정리해본 블로그 포스트임.
+ 사용한 예제 코드
    - <https://github.com/ghjang/personal_study/blob/master/cpp/tuple_cat/main.cpp>
