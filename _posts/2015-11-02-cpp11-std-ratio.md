---
layout: post
title: "[C++11] std::ratio"
description: ""
category: Computer Programming
tags: [C++, C++11]
---
{% include JB/setup %}

std::ratio는 C++11 표준에 추가된 Rational Number(유리수)의 compile-time 계산지원이다. run-time 계산이 아니라는 것에 유의할 것. ratio 내부에 표현되는 분모, 분자 값은 약분된 형태로 값이 유지된다.

{% highlight cpp %}
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
{% endhighlight %}

위 예제에서 볼 수 있는 것과 같이 ratio class template에 넘겨지는 argument값과 ratio 내부에 유지되는 값이 다를 수 있다는 것에 혼동하지 말아야 하겠다. ratio 인스턴스의 타입 자체는 달라도 비율값 자체는 같을 수 있는 상황인 것이다.

---

ratio 내부에서 사용하게되는 compile-time GCD를 구하는 구현부는 한번 살펴 볼만한 것 같다. 이 내용은 다음번 표준에 std::gcd라는 이름의 constexpr 함수로 포함될 수도 있는 모양이다.

---

## 참고
+ [cppreference std::ratio](http://en.cppreference.com/w/cpp/numeric/ratio/ratio)
+ [cppreference std::experimental::gcd](http://en.cppreference.com/w/cpp/experimental/gcd)
+ [https://github.com/ghjang/personal_study/blob/master/cpp/ratio/main.cpp](https://github.com/ghjang/personal_study/blob/master/cpp/ratio/main.cpp)
