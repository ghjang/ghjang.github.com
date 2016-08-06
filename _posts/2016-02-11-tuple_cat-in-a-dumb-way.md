---
layout: post
title: "tuple_cat in a Dumb Way"
description: ""
category: Computer Programming
tags: [C++]
---
{% include JB/setup %}

n개의 tuple 객체들을 하나의 tuple 객체로 합치는 [tuple_cat](http://en.cppreference.com/w/cpp/utility/tuple/tuple_cat) 함수를 직접 작성해보도록 한다. 여기서는 효율성등을 고려하지 않고 기본적인 C++ template 사용법만을 가지고 구현을 시도했다.

다소 복잡한 C++ TMP를 이용해서 효율적인 tuple_cat을 구현하는 것과 관련하여 아래의 글들을 참고할만하다:

+ [Tiny Metaprogramming Library](http://ericniebler.com/2014/11/13/tiny-metaprogramming-library/)
+ [Simple C++11 metaprogramming](http://pdimov.com/cpp2/simple_cxx11_metaprogramming.html)

---

## Take-1: Ordinary Recursion Way

n개의 인자에 대해서 재귀함수호출을 통하여 앞의 인자들부터 끊어서 처리하는 아래와 같은 일반적인 구현을 우선 생각해볼 수 있겠다.

```cpp
```

---

## Take-2: Applying Fold-Left High-Order Function

'주어진 시퀀스의 값을 이항함수를 이용해서 합친다.'

조금만 생각해보면 위의 tuple_cat 구현은 전형적인 fold 알고리즘 패턴임을 알 수가 있다.

NOTE: not written yet.

---

## Take-3: Trying to Use C++17 Built-in Fold Feature

C++17부터는 언어문법 자체에서 fold적인 작업에 대한 지원이 추가되었다.

NOTE: not written yet.

---

## 참고

+ <https://github.com/ghjang/personal_study/blob/master/cpp/tuple_cat/main.cpp>
