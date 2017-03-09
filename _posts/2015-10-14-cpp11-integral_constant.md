---
title: "[C++11] std::integral_constant"
description: ""
category: Computer Programming
tags: [C++, C++11, C++ TMP]
---

정수 값을 타입(type)화 시키는 wrapper 클래스 템플릿이다. `1`, `2`와 같은 정수 값만을 가지고는 (멤버)함수 오버로딩을 할 수가 없다. 정수 값을 타입화 시키면 아래와 같은 코드가 가능하게 된다:

```cpp
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
```

---

## 참고

+ [std::integral_constant](http://en.cppreference.com/w/cpp/types/integral_constant)
+ 사용한 예제 코드
	- <https://github.com/ghjang/personal_study/blob/master/cpp/integral_constant/main.cpp>
