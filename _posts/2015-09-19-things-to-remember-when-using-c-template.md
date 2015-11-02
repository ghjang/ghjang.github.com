---
layout: post
title: "Things to Remember when using C++ Template"
description: "쉽게 잊어버리는, 하지만 기억해야할만한 사항들에 대해서 정리한다."
category: Computer Programming
tags: [C++, C++ TMP]
---
{% include JB/setup %}

쉽게 잊어버리는, 하지만 기억해야할만한 사항들에 대해서 정리한다.

### Template argument deduction시에는 automatic type conversion은 고려되지 않는다.
function template이 아닌 일반 함수의 경우에는 해당 사항이 없다. rank에서 밀려 function template이 배제되는 상황이 발생해 일반 함수가 선택될 수 있다.

생성자를 통한 type conversion, type conversion operator overloading 그리고 derived-to-base implicit type conversion 같은 것이 고려되지 않는다는 것이다.

### Overload resolution시에 rvalue에 대해서 T와 T const&는 동일한 레벨의 perfect match로 취급된다.

### Overload resolution시에 lvalue에 대해서 T와 T &는 동일한 레벨의 perfect match로 취급된다.

### Class template내에 정의된 overload된 assignment operator는 default assignment operator를 대체하지 않는다.
호환가능한(타입변환이가능한) 버전의 overload된 assignment operator가 있을 경우 이것이 기본적인 버전의 것, 그러니까 현재의 template parameter와 완전히 동일한 것을 대체하지 않는다는 것. 이는 default copy constructor와 overload된 template버전의 signature가 동일해보이는 copy constructor의 관계에서도 성립된다는 것.
{% highlight cpp %}
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
{% endhighlight %}

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