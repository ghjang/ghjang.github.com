---
layout: post
title: "[C++14] std::integer_sequence"
description: ""
category: Computer Programming
tags: [C++14, C++ TMP]
---
{% include JB/setup %}

C++14에 추가된 `std::integer_sequence`는 C++ Template metaprogramming에 아주 유용한 도구이다. 하단의 '참고'에 있는 cppreference 링크 글의 내용을 숙지하도록 할 것. 내용이 길지도 않다. 예제에서 보이는 사용법은 일종의 idiom으로 생각하고 익숙해질 필요가 있을 정도다.

표준에서 제공하는 `std::integer_sequence`관련 부분에 한가지 아쉬운 점은 `std::make_integer_sequence`, `std::make_index_sequence`, `std::index_sequence_for`와 같은 sequential한 integer sequence를 생성해주는 helper template은 있으나 임의의 integer sequence를 생성해주는 것은 없다는 점이다. 여기서는 원하는 형태의 integer sequence를 생성하는 한가지 방법에 대해서 적어보도록 하겠다.

---

## Basic Usage

{% highlight cpp %}
using std::is_same;
using std::integer_sequence;
using std::make_integer_sequence;
using std::make_index_sequence;

// ...

using int_seq = make_integer_sequence<std::size_t, 3>;
using index_seq = make_index_sequence<3>;
static_assert(is_same<integer_sequence<std::size_t, 0, 1, 2>, int_seq>(), "");
static_assert(is_same<integer_sequence<std::size_t, 0, 1, 2>, index_seq>(), "");
static_assert(is_same<int_seq, index_seq>(), "");
{% endhighlight %}

`std::integer_sequence`는 이름처럼 특정 integral type의 일정한 개수의 integer sequence를 나타내는 기능을 한다. `std::index_sequence`는 `std::integer_sequence`의 type alias로 integral type이 `std::size_t`로 지정되어있다.

---

## Combination with constexpr Functions

`constexpr` 함수는 constant expression이 요구되는 context에서 사용할 수 있기 때문에 다음과 같은 식의 표현이 가능하다.

{% highlight cpp %}
constexpr int IdentityInt(int i) { return i; }
constexpr int SquareInt(int i) { return i * i; }

// ...

using std::is_same;
using std::integer_sequence;
	
// ...

using identity_int_seq = integer_sequence<int, IdentityInt(0), IdentityInt(1), IdentityInt(2)>;
static_assert(is_same<integer_sequence<int, 0, 1, 2>, identity_int_seq>(), "");

using square_int_seq = integer_sequence<int, SquareInt(0), SquareInt(1), SquareInt(2), SquareInt(3)>;
static_assert(is_same<integer_sequence<int, 0, 1, 4, 9>, square_int_seq>(), "");
{% endhighlight %}

여기서 예로든 `IdentityInt`, `SquareInt`는 매우 단순하기 때문에 해당 부분을 `constexpr` function이 아닌 전통적인 class template을 사용한 수치계산방법을 사용해도 전혀 무리가 없겠다. 하지만 원하는 시퀀스의 값을 계산하는 부분이 복잡해질수록 class template을 이용한 방법은 불편하기는 물론이고 가독성이 너무 떨어지게될 것이다.

---

## A Custom Integer Sequence Example

앞의 예제와 같이 `constexpr` 함수를 원하는 수만큼 일일이 적어주는 것은 실용적이지 못하다. `std::make_index_sequence<4>`와 같은 사용법으로 원하는 수만큼의 squared된 integer sequence를 생성해주는 metafunction을 작성해보면 아래와 같다.

{% highlight cpp %}
constexpr int SquareInt(int i) { return i * i; }

// ...

template <typename IndexSequence>
struct SquareIntegerSequenceImpl;

template <std::size_t... i>
struct SquareIntegerSequenceImpl<std::integer_sequence<std::size_t, i...>>
{
    using type = std::integer_sequence<std::size_t, SquareInt(i)...>;
};

template <std::size_t i>
struct SquareIntegerSequence
        : SquareIntegerSequenceImpl<std::make_index_sequence<i>>
{ };

// ...

using std::is_same;
using std::integer_sequence;

// ...

using square_int_seq = typename SquareIntegerSequence<4>::type;
static_assert(is_same<integer_sequence<std::size_t, 0, 1, 4, 9>, square_int_seq>(), "");
{% endhighlight %}

`SquareIntegerSequence<4>::type`와 같은 표현은 0~3까지의 4개 integer에 대해서 각각 제곱한 integer를 가지는 `std::integer_sequence` type을 리턴하는 것이다.

예제에서 보다시피 '0~(N-1)' 범위의 integer 입력에 대해서 특정 integer값을 리턴하는 `constexpr` function을 정의할 수 있다면 임의의 integer sequence를 쉽게 생성할 수 있을 것이다. 'y=f(x)' 같은 수학표현을 떠올려보는 것도 재미있을 것이다. :-)

---

## A Generic Custom Integer Sequence Example

앞에서 설명한 `SquareIntegerSequence` 예의 경우는 제곱된 integer sequence만을 다루는 것이다. 이를 좀더 일반화해서 다시 작성해본 것이 아래이다.

{% highlight cpp %}
constexpr int SquareInt(int i) { return i * i; }

template <std::size_t i>
struct SquareIntGenerator
{
    constexpr static std::size_t value = SquareInt(i);
};

// ...

template <typename IndexSequence, template<std::size_t> class GeneratorFunc>
struct CustomIntegerSequenceImpl;

template <std::size_t... i, template<std::size_t> class GeneratorFunc>
struct CustomIntegerSequenceImpl<
            std::integer_sequence<std::size_t, i...>,
            GeneratorFunc
        >
{
    using type = std::integer_sequence<std::size_t, GeneratorFunc<i>::value...>;
};

template <std::size_t i, template<std::size_t> class GeneratorFunc>
struct CustomIntegerSequence
        : CustomIntegerSequenceImpl<std::make_index_sequence<i>, GeneratorFunc>
{ };

template <std::size_t i, template<std::size_t> class GeneratorFunc>
using MakeCustomIntegerSequence = typename CustomIntegerSequence<i, GeneratorFunc>::type;

// ...

using std::is_same;
using std::integer_sequence;

// ...

using square_int_seq = typename CustomIntegerSequence<4, SquareIntGenerator>::type;
static_assert(is_same<integer_sequence<std::size_t, 0, 1, 4, 9>, square_int_seq>(), "");

using square_int_seq_1 = MakeCustomIntegerSequence<4, SquareIntGenerator>;
static_assert(is_same<integer_sequence<std::size_t, 0, 1, 4, 9>, square_int_seq_1>(), "");
static_assert(is_same<square_int_seq, square_int_seq_1>(), "");
{% endhighlight %}

여기서 `MakeCustomIntegerSequence<4, SquareIntGenerator>`와 같은 표현은 0~3 범위의 4개 정수에 대해서 template template parameter로 넘겨진 `SquareIntGenerator`를 이용하여 제곱한 값으로 구성된 `std::integer_sequence` type을 리턴하는 것이다.

`constexpr` 함수인 `SquareInt`를 `MakeCustomIntegerSequence`의 두번째 template parameter의 argument로 지정하지 않은 것은 그렇게 할 수가 없기 때문이다. `SquareInt` 자체는 constant expression도 아니고 type도 아니기에 template parameter에 지정할 수 없다.(방법을 못 찾은 것인가?) `SquareInt`의 type을 얻을 수는 있지만 `SquareInt`를 호출하는 코드를 `CustomIntegerSequence`내에서 작성할 수 없었다. 해서 중간 계층인 `SquareIntGenerator`를 귀찮지만 도입한 것이다. :-(

다른 정수열을 생성하는 예제를 보이기 위해서 아래 코드를 추가로 작성해 보았다.

{% highlight cpp %}
constexpr int Factorial(int i)
{
    if (i == 0) {
        return 1;
    }
    return i * Factorial(i - 1);
}

template <std::size_t i>
struct FactorialGenerator
{
    constexpr static std::size_t value = Factorial(i);
};

// ...

using factorial_int_seq = MakeCustomIntegerSequence<5, FactorialGenerator>;
static_assert(is_same<integer_sequence<std::size_t, 1, 1, 2, 6, 24>, factorial_int_seq>(), "");
{% endhighlight %}

`Factorial` 함수 정도는 class template으로 수치계산용 metafunction으로 작성하는 것이 간단하기는 하지만 가독성면에서는 `constexpr` 함수형태가 훨씬 좋다.

좀더 복잡한 형태의 `constexpr` 함수 예제는 귀찮아져서 일단 본 글에서는 접도록 한다.

---

## Where to Use

이런 compile time integer sequence type을 생성해서 어디다가 써먹을 수 있을까 생각이 들 수 있을 것이다. 건 상상하기 나름이리라. :-)

간단하게는 이런 걸 할 수 있을 것이다:

+ '홀수/짝수' 인덱스열을 생성하고 variadic template arguments에서 '홀수/짝수'번째 것만 뽑아서 처리
+ 역순의 인덱스열을 생성해서 reverse된 tuple type 생성하기. 예를 들면 `std::tuple<char, int, float>`로부터 `std::tuple<float, int, char>` type을 생성.
+ variadic template arguments에 대해서 boolean 형태의 정수열을 생성하고 true 형태인 것만 처리. 'type list filter'같은 것을 만드는데 이용할 수 있겠다는 것이다.
+ Music Theory의 Scale개념을 수의 열로 추상화해서 특정 Scale에 해당하는 Musical Note들의 열을 구하거나 하는 따위의 일도 가능할 것 같다.
+ ...

---

## 참고
+ [cppreference std::integer_sequence](http://en.cppreference.com/w/cpp/utility/integer_sequence): 예제 포함 전체 내용을 숙지하는 것이 좋겠다. 예제가 좋다.
+ <https://github.com/ghjang/personal_study/blob/master/cpp/integer_sequence/main.cpp>
+ <https://github.com/ghjang/rocky/blob/master/rocky/meta/IntegerSequenceUtility.h>
+ <https://github.com/ghjang/rocky/blob/master/rocky/test/IntegerSequenceUtilityTest.cpp>