---
layout: post
title: "[C++11] std::integral_constant"
description: ""
category: Computer Programming
tags: [C++, C++11, C++ TMP]
---
{% include JB/setup %}

정수형 타입의 값을 type화 할 수 있도록 해주는 wrapper 클래스 템플릿이다.

'1', '2'와 같은 정수형 값만을 가지고는 (멤버)함수 오버로딩을 할 수가 없다. 정수형 값을 type화 시키면 아래와 같은 코드가 가능하게 된다:

<link rel="stylesheet" href="~/highlight/styles/default.css">
<script src="~/highlight/highlight.pack.js"></script>
<script>hljs.initHighlightingOnLoad();</script>

<pre>
<code class='cpp'>
template <typename T, int N>
void doSomething(T t, std::integral_constant<int, N>)
{
    std::cout << "doSomething(T t, std::integral_constant<int, N>) is called: " << N << std::endl;
};

template <typename T>
void doSomething(T t, std::integral_constant<int, 2>)
{
    std::cout << "doSomething(T t, std::integral_constant<int, 2>) is called." << std::endl;
};

//...

// function overloading by integral_constant
{
	struct s { };
	doSomething(s(), integral_constant<int, 0>());
	doSomething(s(), integral_constant<int, 1>());
	doSomething(s(), integral_constant<int, 2>()); // the second function is called.
	doSomething(s(), integral_constant<int, 3>());
}
</code>
</pre>

## 참고
+ [cppreference std::integral_constant](http://en.cppreference.com/w/cpp/types/integral_constant)
+ [std::integral_constant test sample code](https://github.com/ghjang/personal_study/blob/master/cpp/integral_constant/main.cpp)
