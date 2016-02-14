---
layout: post
title: "[C++11] std::initializer_list"
description: ""
category: Computer Programming
tags: [C++, C++11]
---
{% include JB/setup %}

C++11의 brace initialization 표기법의 결과물이 std::initializer_list라고 함. 이때 리스트의 원소 타입은 자동으로 deduction된다 함. 내용이 간단한 편이기 때문에 하단의 레퍼런스 링크를 참고하면 되겠다.

간단히 기억해야할만한 사항은 다음과 같다.

+ initializer_list 인스턴스 내부에 배열이 생성되는 형태라고 함. 인스턴스내 배열의 lifetime은 해당 initializer_list보다 길다고 보면 안되겠음.
+ STL 컨테이너와 같이 copy-by-value 개념임.
+ 표준 STL 컨테이너는 물론이고 많은 라이브러리 부분에 initializer_list를 인자로 받도록 overloading되어 있음.
+ (지금 시점에서는 정확한 이유를 설명을 할 수 없으나, auto가 사용되는 context에서는 문제가될 수 있는 상황이 있는가 봄. 다음번 표준에 수정을 한다던가 그러함. auto context에서는 사용을 피하는 것이 좋겠음.)

```cpp
template <typename T>
void print(std::initializer_list<T> const& l)
{
    for (T const& e: l) {
        std::cout << e << std::endl;
    }
}


template <typename T>
struct Container
{
    Container(std::initializer_list<T> const& l)
           : data_(l)
    { }

    void add(std::initializer_list<T> const& l)
    {
        data_.insert(data_.end(), l.begin(), l.end());
    }

    std::vector<T> data_;
};

//...

using std::is_same;
using std::initializer_list;
using std::cout;
using std::endl;

// compile error. This is not a valid expression. It's just a kind of comma-separated integers with braces, which is not valid.
//{ 1, 2, 3 };

// compile error, 'expected expression'.
//decltype({ 1, 2, 3 });

// the list is treated as an initializer list in this auto context.
auto ints = { 1, 2, 3 };
static_assert(is_same<decltype(ints), initializer_list<decltype(1)>>::value, "");
static_assert(is_same<decltype(ints), initializer_list<int>>::value, "");
for (auto i: ints) {
	cout << i << endl;
}

// the list is treated as an initializer list in this auto context.
for (auto i: { 4, 5, 6 }) {
	cout << i << endl;
}

// compile error. 'can't deduce the type for the initializer_list.'.
//auto mixed = { 7, 8, 9.0 };

print({ 7, 8, 9 });

cout << "---" << endl;

Container<int> c{ 10, 11, 12 };
c.add({ 13, 14, 15 });
for (auto i: c.data_) {
	cout << i << endl;
}
```

---

## 참고
+ [cppreference std::initializer_list](http://en.cppreference.com/w/cpp/utility/initializer_list)
+ [https://github.com/ghjang/personal_study/blob/master/cpp/initializer_list/main.cpp](https://github.com/ghjang/personal_study/blob/master/cpp/initializer_list/main.cpp)
