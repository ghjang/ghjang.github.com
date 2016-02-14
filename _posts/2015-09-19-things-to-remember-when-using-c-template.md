---
layout: post
title: "Things to Remember when Using C++ Template"
description: "쉽게 잊어버리는, 하지만 기억해야할만한 사항들에 대해서 정리한다."
category: Computer Programming
tags: [C++, C++ TMP]
---
{% include JB/setup %}

**NOTE: 정리가 제대로되어 있지 않음. 틀린 내용도 포함되어 있을 수 있음.**

---

쉽게 잊어버리는, 하지만 기억해야할만한 사항들에 대해서 정리한다.

### Template type deduction시에는 automatic type conversion은 고려되지 않는다.
function template이 아닌 일반 함수의 경우에는 해당 사항이 없다. rank에서 밀려 function template이 배제되는 상황이 발생해 일반 함수가 선택될 수 있다.

생성자를 통한 type conversion, type conversion operator overloading 그리고 derived-to-base implicit type conversion 같은 것이 고려되지 않는다는 것이다.

### Overload resolution시에 rvalue에 대해서 T와 T const&는 동일한 레벨의 perfect match로 취급된다.

### Overload resolution시에 lvalue에 대해서 T와 T &는 동일한 레벨의 perfect match로 취급된다.

### Class template내에 정의된 overload된 assignment operator는 default assignment operator를 대체하지 않는다.
호환가능한(타입변환이가능한) 버전의 overload된 assignment operator가 있을 경우 이것이 기본적인 버전의 것, 그러니까 현재의 template parameter와 완전히 동일한 것을 대체하지 않는다는 것. 이는 default copy constructor와 overload된 template버전의 signature가 동일해보이는 copy constructor의 관계에서도 성립된다는 것.

```cpp
struct X
{
	// ...
	
	template<typename T>
	X(const T&); // default copy constructor를 대체하지 않음. constructor의 overload set에 포함될 뿐.
	
	// ...
	
	template<typename T>
	X & operator = (const T&); // default assignment operator를 대체하지 않음. assignment operator의 overload set에 포함될 뿐.
	
	// ...
};
```

### 인자순서, perfect match

### function template instance는 또다른 function template의 argument deduction에 그냥 쉽게 사용될 수는 없다.
현재의 표준이 그러한지는 확인해볼 필요가 있겠음. STL 알고리즘 중에 functor를 받도록 되어있는 것들이 있다. 이 인자에 어떤 function template의 instance를 넘길 경우 컴파일이 실패한다는 것. 컴파일을 성공시키기 위해서는 명시적인 함수 포인터 캐스팅등이 필요하다.

### class template partial specialization시에 모호성이 발생해서 컴파일이 실패할 경우 좀더 세분화된 specialization을 추가할 필요가 있다.

### typeid의 리턴타입은 const std::type_info& 
Loki factory, double dispatch 관련 기능은 type_info wrapper 클래스를 활용한다.

### template, implicit interface
valid expression. implicit하게 구성되는 요구 인터페이스들로 컴파일타임 다형성

### template template parameter내에서 사용된 parameter name은 해당 template을 인자로 받는 template내에서는 사용할 수 없다.

### local class type은 template의 argument로 사용할 수 없다.

### member function template으로는 부모 클래스의 virtual function을 override할 수 없다.
또한 member function template으로 virtual function을 선언할 수도 없다.

### friend
friend가 definition이 되어버리는 상황등에 대해서 정리.

### full class template specialization에서는 상수값을 직접 적을 수 있으나, partial class template specialization에서는 type만 적을 수 있다(???). constant value라면 무엇이든 적을 수 있다(?).
rocky/meta/ReverseTuple.h에서 확인할 것.
TODO: 매번 혼동하게되는 부분으로 예제를 작성하는게 좋겠다.

### partial class template specialization의 template parameter에는 default argument를 설정할 수 없다.

### array element type은 'void, reference, function'이 될 수 없다.

### type을 요구하는 곳에 template을 넘길 수 없다.

### metafunction class를 사용하면 template의 유용성이 좀더 높아진다(?).
+ metafunction class는 Apply라는 이름의 member class template을 가지고 있는 class type을 말한다.
+ metafunction class 자체가 class template일 수도 있다. 이 경우 metafunction class에 넘겨지는 parameter가 내부 member class template과 연계되는게 일반적이다. 새로운 동작을 하는 template을 return하는 효과를 나타낸다.
+ Quote helper metafunction class 는 std::is_integral과 같은 일반 metafunction을 metafunction class로 바꾸어주는 일을 한다. Quote를 사용하지 않는다면 해당 metafuncion을 wrapping하는 일반 class type을 일일이 작성해 주어야 한다. 호출자측에서 약간 코딩이 늘어나는(?) 측이 있기는 하다. 
+ template template paramete를 일반 type parameter로 바꿀 수 있기 때문에 template parameter 표기부분이 짧아 진다.면
+ metafunction class를 인자로 사용하도록 작성된 high-order function의 사용자측 코딩이 간편해 진다.
+ ApplyT 같은 type alias를 사용하면 metafunction class를 사용하는 high-order function에서 코드를 좀 줄일 수 있다.
+ metafunction class 개념을 사용하면, metafunction composition을 하기가 C++상에서 좀 쉬워(?)진다.

### template <template <typename...> class f> struct Quote; 형태에서 f는 임의의 class template에 매칭될 수 있다.

### type alias template은 specialization을 가질수가 없다.
