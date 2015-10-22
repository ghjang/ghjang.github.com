---
layout: post
title: "[C++] Polymorphism and Variant"
description: ""
category: Computer Programming
tags: [C++, Visitor Pattern, Design Pattern]
---
{% include JB/setup %}

C++에서 사용할 수 있는 virtual function기반의 dynamic polymorphism과 template기반의 static polymorphism에 대해서 몇자 적고자 한다. polymorphism하면 보통 virtual function기반의 dynamic polymorphism만을 생각하기도 한다. 하지만, 특정 부분에 있어서 동일 인터페이스를 가지고 함수호출등의 어떤 동작을 했을때 결과가 다르게 나타난다면 이를 polymorphic하다고 할 수도 있겠다. 좀더 넓은 의미에서 생각해볼 필요가 있겠다.

---

## Dynamic Polymorphism through Virtual Function

다음은 도형 2개(원, 정사각형)를 C++ 클래스 상속을 이용하여 간단히 모델링 한 예제코드이다.
{% highlight cpp %}
struct Point
{
	Point(int x, int y)
		: x_(x), y_(y)
	{ }

	Point()
		: Point(0, 0)
	{ }

	double x_;
	double y_;
};

struct Shape
{
	virtual double area() const = 0;
	virtual double perimeter() const = 0;
};

class Circle: public Shape
{
private:
	Point centerPoint_;
	double radius_;

public:
	Circle(Point pt, double r)
		: centerPoint_(pt), radius_(r)
	{ }

	Circle()
		: Circle(Point(), 0)
	{ }

public:
	double area() const override
	{
		return M_PI * radius_ * radius_;
	}

	double perimeter() const override
	{
		return 2 * M_PI * radius_;
	}
};

class Square: public Shape
{
private:
	Point leftTop_;
	double sideLength_;

public:
	Square(Point lt, double len)
		: leftTop_(lt), sideLength_(len)
	{ }

	Square()
		: Square(Point(), 0)
	{ }

public:
	double area() const override
	{
		return sideLength_ * sideLength_;
	}

	double perimeter() const override
	{
		return 4 * sideLength_;
	}
};
{% endhighlight %} 
면적(area)과 둘레길이(perimeter)를 구하는 함수를 virtual function으로 구현하였다. Circle와 Square 클래스는 Shape 부모 클래스에서 상속하여 tightly-coupled 되었다. 이 것이 의미하는 것은 Shape 상에서의 변화가 자식클래스들 모두에게 영향을 미칠 수 있다는 것이다. 점차 클래스 계층이 복잡해지면 관리가 힘들어진다는 것을 의미한다.

클라이언트 코드에서는 아래와 같이 호출하여 사용하겠다.
{% highlight cpp %}
// ...

// heterogeneous object creation
using ShapePtr = std::shared_ptr<Shape>;
using std::make_shared;
std::vector<ShapePtr> sv = {
		make_shared<Circle>(Point(), 5),
		make_shared<Square>(Point(), 5),
		make_shared<Circle>(Point(), 10)
};

// ...

// virtual function call
for (auto s : sv) {
	std::cout << "area: " << s->area() << '\n'
				<< "perimeter: " << s->perimeter()
				<< std::endl;
}
{% endhighlight %}
부모 클래스의 인터페이스, 그러니까 포인터를 가지고 이종의 자식 객체들을 단일한 방식으로 다룰 수 있게 된다. 부모 클래스의 인터페이스로 다루기에 해당 부분에서는 실제 다루는 객체의 정보를 알 수가 없다. concret type 정보를 잃었다는 것이다. 경우에 따라서는 그다지 보기 좋지 않은 가능하면 피하라고 가이드되는 dynamic down-cast를 해야할 수도 있다.

---

## Static Polymorphism through Template

앞서 살펴본 dynamic polymorphism에서 사용한 동일 코드 내용을 이번에는 C++ template만을 사용해서 표현해보면 아래와 같다.
{% highlight cpp %}
struct Point
{
	Point(int x, int y)
			: x_(x), y_(y)
	{ }

	Point()
			: Point(0, 0)
	{ }

	double x_;
	double y_;
};

class Circle
{
private:
	Point centerPoint_;
	double radius_;

public:
	Circle(Point pt, double r)
			: centerPoint_(pt), radius_(r)
	{ }

	Circle()
			: Circle(Point(), 0)
	{ }

public:
	double area() const
	{
		return M_PI * radius_ * radius_;
	}

	double perimeter() const
	{
		return 2 * M_PI * radius_;
	}
};

class Square
{
private:
	Point leftTop_;
	double sideLength_;

public:
	Square(Point lt, double len)
			: leftTop_(lt), sideLength_(len)
	{ }

	Square()
			: Square(Point(), 0)
	{ }

public:
	double area() const
	{
		return sideLength_ * sideLength_;
	}

	double perimeter() const
	{
		return 4 * sideLength_;
	}
};
{% endhighlight %}
dynamic 버전과 차이는 Shape 부모 클래스가 제거되었다는 것이다. 이 것이 의미하는 것은 Circle과 Square 자체만 보면 별다른 관계가 없다. loosely-coupled 되었다는 것이다. area, perimeter 멤버함수의 signature는 편의상 dynamic 버전의 것을 그대로옮겨왔다. 하지만 loosely-coupled되었기 때문에 꼭 그럴 필요는 없다. 예를 들어서 area 멤버함수에 붙은 'const'를 Circle 클래스 한쪽에서 제거해도 Square쪽에 당연히 영향이 없다.

클라이언트 코드에서는 아래와 같이 사용할 수 있겠다.
{% highlight cpp %}
// ...

template <typename Shape>
void runShapeTest()
{
	// homogeneous object creation
	using ShapePtr = std::shared_ptr<Shape>;
	using std::make_shared;
	std::vector<ShapePtr> sv = {
			make_shared<Shape>(Point(), 5),
			make_shared<Shape>(Point(), 10)
	};

	// normal function call
	for (auto s : sv) {
		std::cout << "area: " << s->area() << '\n'
		          << "perimeter: " << s->perimeter()
		          << std::endl;
	}
}
	
// ...

runShapeTest<Circle>();
runShapeTest<Square>();
{% endhighlight %}
runShapeTest 함수 자체는 다루고자하는 도형을 template parameter type을 이용하여 instantiation되어서 polymorphic하게 동작한다. area, perimeter 멤버함수를 호출하는 부분이 완전히 dynamic 버전과 동일하다. 하지만, dynamic 버전에서와 같이 이종의 객체를 한번에 다룰 수는 없게 된다.

### Variant

---

## When They Are All Mixed Together

---

## 참고
+ [Boost.Variant](http://www.boost.org/doc/libs/1_59_0/doc/html/variant.html)
