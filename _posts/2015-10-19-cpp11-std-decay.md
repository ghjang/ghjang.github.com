---
layout: post
title: "[C++11] std::decay"
description: "std::decay에 대해서 알아본다."
category: Computer Programming
tags: [C++, C++11, C++ TMP]
---
{% include JB/setup %}

decay는 사전적으로 '부식'이라는 의미이다. C++에서 decay란 특정 context에서 type이 원래 고유의 것이 아닌 다른 type으로 변질되는 것을 말한다.

---

## array-to-pointer decay
가장 대표적인 decay의 예가 array-to-pointer decay이다:
{% highlight cpp %}
using std::is_same;
using std::decay;   // C++11
using std::decay_t; // C++14

// ...

// int array to pointer decay
int a[3] = { 1, 2, 3 };
int * pa = a;
static_assert(is_same<decltype(a), int[3]>::value, "");
static_assert(is_same<decltype(pa), int *>::value, "");
static_assert(is_same<decltype(pa), decay<decltype(a)>::type>::value, "");
static_assert(is_same<decltype(pa), decay_t<decltype(a)>>::value, "");
{% endhighlight %}

array-to-pointer decay가 일어난 문맥에서는 배열의 최상위 차원 크기를 알 수 없게된다. 1차원 배열이 가장 많이 사용되게되는데, 포인터 변수만을 놓고 보면 해당 포인터가 가리키는 것이 단일객체인지 배열인지 알 수 없다. 보통 배열과 배열크기정보를 같이 전달하여 처리하는 패턴이 된다. 전되달는 크기정보가 잘못되었거나 범위를 넘어가는 인덱스 조작을 통해서 문제가 발생할 수 있게된다.

다차원 배열에서의 decay 예는 다음과 같은 형태가 된다:
{% highlight cpp %}
// multi-dimensional array to pointer decay
int aa[2][3] = {
		{ 1, 2, 3 },
		{ 4, 5, 6 }
};
int (* paa) [3] = aa; // looses top-level dimension size information, 2.
static_assert(is_same<decltype(aa), int [2][3]>::value, "");
static_assert(is_same<decltype(paa), int (*) [3]>::value, ""); // pointer to 'int [3]'
paa[1][0] = 44;
assert(aa[1][0] == 44);
{% endhighlight %}

---

## function-to-pointer decay
늘상 사용하는 당연히 생각되는 또다는 decay는 function-to-pointer decay이다. 

---

## Function template에 인자로 전달시 decay


---

###
참고
+ 
