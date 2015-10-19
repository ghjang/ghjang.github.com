---
layout: post
title: "[C++11] std::decay"
description: "std::decay에 대해서 알아본다."
category: Computer Programming
tags: [C++, C++11, C++ TMP]
---
{% include JB/setup %}

decay는 사전적으로 '부식'이라는 의미이다. C++에서 decay란 특정 context에서 type이 원래 고유의 것이 아닌 다른 type으로 변질되는 것을 말한다. 가장 대표적인 예가 array-to-pointer decay이다:
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

---

###
참고
+ 
