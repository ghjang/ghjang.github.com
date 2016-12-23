---
title: "[C++11] std::ratio"
description: ""
category: Computer Programming
tags: [C++, C++11]
---

std::ratio는 C++11 표준에 추가된 Rational Number(유리수)의 compile-time 계산지원이다. run-time 계산이 아니라는 것에 유의할 것. ratio 내부에 표현되는 분모, 분자 값은 약분된 형태로 값이 유지된다.

```cpp
using std::is_same;
using std::ratio;
using std::ratio_add;
using std::ratio_multiply;
using std::ratio_equal;
using std::cout;

using two_third = ratio<2, 3>;
cout << two_third::num << '/' << two_third::den << std::endl;   // 2/3

using four_sixth = ratio<4, 6>;
cout << four_sixth::num << '/' << four_sixth::den << std::endl; // 2/3

static_assert(
		!is_same<two_third, four_sixth>::value,
		"two_third and four_sixth are not the same type."
);
static_assert(
		(two_third::num == four_sixth::num) && (two_third::den == four_sixth::den),
		"but two_third and four_sixth have the same numerator and denominator values."
);
static_assert(ratio_equal<two_third, four_sixth>::value, "two_third and four_sixth have same ratio.");

using four_third = ratio_add<two_third, two_third>;
static_assert(is_same<four_third, ratio<4, 3>>::value, "");
static_assert(ratio_equal<four_third, ratio<4, 3>>::value, "");

using eight_sixth = ratio_multiply<four_sixth, ratio<2>>;
static_assert(is_same<eight_sixth, ratio<4, 3>>::value, ""); // reduced to the lowest terms.
static_assert(ratio_equal<eight_sixth, ratio<4, 3>>::value, "");
```

위 예제에서 볼 수 있는 것과 같이 ratio class template에 넘겨지는 argument값과 ratio 내부에 유지되는 값이 다를 수 있다는 것에 혼동하지 말아야 하겠다. ratio 인스턴스의 타입 자체는 달라도 비율값 자체는 같을 수 있는 상황인 것이다.

---

ratio 내부에서 사용하게되는 compile-time GCD를 구하는 구현부는 한번 살펴 볼만한 것 같다. 이 내용은 다음번 표준에 std::gcd라는 이름의 constexpr 함수로 포함될 수도 있는 모양이다.

---

### How to Create a Compile-time Constant String from std::ratio

std::cout등에 출력하기 위한 std::ratio의 string 값을 생성하는 모듈을 만드는 것이 목적이다.

간단할 줄 알고 도전했는데, 구현이 생각보다 매우 복잡하다. :-( 다음의 CppCon2015 자료를 참고하였다.

+ [Variable Templates - C++14 compile-time computation - Peter Sommerlad](https://github.com/CppCon/CppCon2015/tree/master/Presentations/Variable%20Templates%20-%20C%2B%2B14%20compile-time%20computation)

완전히 동일한 코드를 사용할 수는 없었고 나름 좀더 살을 붙였다. 일부는 재사용이 가능할 것도 같다. 일단 여기서는 상세 설명은 배제하고 구현 코드만 붙이도록 하겠다. 전체 코드는 하단의 참고 링크를 살펴보도록 할 것.

다음번에 **constexpr** 관련 포스트를 별도로 작성하여 설명하는 것이 맞을 것으로 보인다.

```cpp
constexpr uint8_t get_number_of_digit(intmax_t i)
{
    if (i < 0) {
        i = -i;
    }
    if (i < 10) {
        return 1;
    }
    uint8_t n = 1;
    for ( ; ; ) {
        ++n;
        if (i / 10 < 10) {
            break;
        }
        i /= 10;
    }
    return n;
}

constexpr char get_digit_char(intmax_t n, uint8_t numOfDigit, uint8_t digitIndex)
{
    intmax_t modNum = 1;
    for (uint8_t i = 0; i < digitIndex; ++i) {
        modNum *= 10;
    }
    n %= modNum;

    for (uint8_t i = 0; i < digitIndex - 1; ++i) {
        n /= 10;
    }

    return n + '0';
}

constexpr intmax_t get_abs(intmax_t i)
{
    return (i < 0) ? -i : i;
}


template <char... s>
constexpr static char const char_array[] = { s..., '\0' };

template <char... s>
using char_sequence = std::integer_sequence<char, s...>;


template <intmax_t n, typename IndexSequence>
struct make_char_sequence;

template <intmax_t n, std::size_t... indices>
struct make_char_sequence<n, std::index_sequence<indices...>>
{
    using type = char_sequence<
                    get_digit_char(n, get_number_of_digit(n), sizeof...(indices) - indices)...
                 >;
};


template <char... s>
constexpr auto make_char_array(char_sequence<s...>, bool isNegative)
{
    if (isNegative) {
        return char_array<'-', s...>;
    }
    return char_array<s...>;
}


//...


template <typename Sequence1, typename Sequence2>
struct concat_sequence;

template <char... s1, char... s2>
struct concat_sequence<char_sequence<s1...>, char_sequence<s2...>>
{
    using type = char_sequence<s1..., s2...>;
};


template <std::intmax_t Num, std::intmax_t Denom>
constexpr auto to_str(std::ratio<Num, Denom>)
{
    using num_indices_t = std::make_index_sequence<get_number_of_digit(Num)>;
    using num_char_sequence_t = typename make_char_sequence<get_abs(Num), num_indices_t>::type;

    using denom_indices_t = std::make_index_sequence<get_number_of_digit(Denom)>;
    using denom_char_sequence_t = typename make_char_sequence<get_abs(Denom), denom_indices_t>::type;

    using temp_char_array_t = typename concat_sequence<num_char_sequence_t, char_sequence<'/'>>::type;
    using final_char_array_t = typename concat_sequence<temp_char_array_t, denom_char_sequence_t>::type;

    return make_char_array(final_char_array_t(), (Num < 0 && Denom > 0) || (Num > 0 && Denom < 0));
}


//...

using std::ratio;
using std::string;
using std::cout;
using std::endl;

//...

using two_third = ratio<2, 3>;
constexpr auto two_third_str = to_str(two_third());
cout << two_third_str << endl;
assert(string("2/3") == two_third_str);

using minus_two_third = ratio<-2, 3>;
constexpr auto minus_two_third_str = to_str(minus_two_third());
cout << minus_two_third_str << endl;
assert(string("-2/3") == minus_two_third_str);

using minus_two_third1 = ratio<2, -3>;
constexpr auto minus_two_third_str1 = to_str(minus_two_third1());
cout << minus_two_third_str1 << endl;
assert(string("-2/3") == minus_two_third_str1);
```

---

## 참고
+ [cppreference std::ratio](http://en.cppreference.com/w/cpp/numeric/ratio/ratio)
+ [cppreference std::experimental::gcd](http://en.cppreference.com/w/cpp/experimental/gcd)
+ [https://github.com/ghjang/personal_study/blob/master/cpp/ratio/main.cpp](https://github.com/ghjang/personal_study/blob/master/cpp/ratio/main.cpp)
