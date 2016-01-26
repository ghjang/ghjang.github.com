---
layout: post
title: "About ForEachArgument Function Template"
description: ""
category: Computer Programming
tags: [C++, C++11, C++14, C++17, C++ TMP]
---
{% include JB/setup %}

최근 보게된 다음 동영상에서 소개된 'for_each_argument'라는 function template과 관련하여 몇자 적는다. 해당 설명은 동영상내에 매우 자세하게 설명되어 있다. 여기서는 직접 for_each_argument를 구현한다고 했을 경우 최종 결과물에 이르기까지의 과정에 대해서 나름데로 적어본다. 결과물의 코드를 이미 알고 있기 때문에 좀 작위적인 부분들이 있겠다. for_each_argument는 자체적으로도 의미가 있겠으나 또다른 기능을 구현하는데 훌륭한 빌딩블럭으로써 사용될 수 있겠다.

+ [Dive into C++14 - 2 - for_each_argument explained and expanded](https://www.youtube.com/watch?v=Za92Tz_g0zQ)

---

for_each_argument를 직접 구현한다면, 우선 별다른 생각없이 다음과 같게 구현을 시도해 볼수가 있겠다.
 
{% highlight cpp %}
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
   f(args)...;
   return f;
}
{% endhighlight %}

하지만 이코드는 아래와 같은 컴파일 오류를 발생시킨다.
<pre>
<code>
error: expression contains unexpanded parameter pack 'args'
</code>
</pre>
위와 같은 표현으로는 parameter pack을 확장할 수 없다는 것이다.

현재의 C++11/14 표준에서 parameter pack이 확장 가능한 context를 정확하게 모른다고했을 경우, 좀더 머리를 굴려보면 다음과 같은 것들을 시도해볼 수도 있을 것이다.

{% highlight cpp %}
// another try-1
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
    (f(args), 0)...;
    return f;
}

// ...

// another try-2
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
    (f(args))...;
    return f;
}

// ...

// another try-3
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
    { f(args)... };
    return f;
}
{% endhighlight %}

하지만 위의 세가지 경우 모두 이전과 같이 parameter pack을 확장할 수 없다는 동일 컴파일 오류를 발생시킨다.

다음으로 brace initialization내에서 확장도 시도해본다.

{% highlight cpp %}
// parameter pack expansion by using brace initialization - 1
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
    auto il = { f(args)... };
    return f;
}

// ...

// parameter pack expansion by using brace initialization - 2
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
    std::initializer_list<int> il = { f(args)... };
    return f;
}
{% endhighlight %}

이 경우는 정상적으로 컴파일된다. brace initialization에서 parameter pack의 확장이 허용된다. 두번째 코드에서 `int`를 인자로 하는 `std::initializer_list<int>`를 명시적으로 사용한 것은 최초에 ForEachArgument 테스트로 작성한 코드가 대략 다음과 같기 때문이다.

{% highlight cpp %}
struct Sum
{
    Sum() : s_{} { }
    int operator () (int i)
    {
        s_ += i;
        return s_;
    }
    int s_;
};

// ...

ForEachArgument(Sum(), 1, 2, 3, 4, 5);
{% endhighlight %}

auto context에서 brace initialization의 사용은 현재 표준상에서 문제가 있을 수 있는 경우가 있다고 어디서 들었던 기억이 있어서 두번째 것을 사용하도록 한다. 헌데 이 코드내에는 사용되지 않는 지역변수 `il`이 1개 있다. redundant한 이름이기에 변수 이름을 제거해서 다음과 같이 이름없는 객체를 생성하는 것으로 코드를 변경한다.

{% highlight cpp %}
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
    std::initializer_list<int> { f(args)... };
    return f;
}
{% endhighlight %}

이 경우 컴파일은 성공하나 아래와 같은 컴파일 경고 메시지가 나타난다.
<pre>
<code>
warning: expression result unused [-Wunused-value]
</code>
</pre>

제대로된 생각을 가진 C++ 프로그래머라면 컴파일 경고는 무시하면 안됨으로 경고를 없애려 시도한다. 이 경우 유용한 `(void)` casting을 시도하도록 코드를 다시 수정한다.

{% highlight cpp %}
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
    (void) std::initializer_list<int> { f(args)... };
    return f;
}
{% endhighlight %}

여기까지하면 정상적으로 동작한다. 테스트도 통과한다.

하지만 f가 int를 리턴하지 않을 경우 문제된다. void가 리턴될 수도 있고 int와 호환되지 않는 값이 리턴될 수도 있다. 그러니까 다음과 같은 형태의 테스트를 추가하면 곧바로 컴파일 오류가 발생하게 된다.

{% highlight cpp %}
struct Sum1
{
    Sum1() : s_{} { }
    void operator () (int i) // void return type
    {
        s_ += i;
    }
    int s_;
};

// ...

ForEachArgument(Sum1(), 1, 2, 3, 4, 5);
{% endhighlight %}

오류 메시지는 대략 다음과 같다.
<pre>
<code>
error: cannot initialize an array element of type 'const int' with an rvalue of type 'void'
</code>
</pre>

이 문제를 해결하기 위해서 comma(,) 연산자를 도입한다.

{% highlight cpp %}
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
    (void) std::initializer_list<int> { (f(args), 0)... };
    return f;
}
{% endhighlight %}

comma 연산자의 간단한 예를들면, `a, b`라고 하는 표현이 있을 때 전체 표현식 그러니까 `(a, b)`의 결과값은 `b`이다. 위 코드의 `(f(args), 0)`부분은 결과값이 `0`이다. `f`의 리턴 값은 무시되기 때문에 리턴타입이 무엇이던지간에 상관이 없다.

여기까지하면 parameter pack을 확장하면서도 컴파일 경고가 없는 (깔끔한) 코드가 만들어지게 된다. 테스트들도 모두 통과한다.

효율성을 고려하여 전달되는 인자를 받는 변수와 리턴 타입을 업데이트하고 마무리한다.

{% highlight cpp %}
template <typename F, typename... Args>
decltype(auto) ForEachArgument(F && f, Args &&... args)
{
    (void) std::initializer_list<int> { (f(std::forward<Args>(args)), 0)... };
    return std::forward<F>(f);
}
{% endhighlight %}

앞서 작성한 `struct`를 사용한 functor는 물론이고 다음과 같은 lambda 표현식을 사용한 테스트도 정상적으로 동작한다.

{% highlight cpp %}
int intSum = 0;
ForEachArgument([&intSum](int i) { intSum += i; }, 1, 2, 3, 4, 5);
REQUIRE(intSum == 15);  // refer to catch.hpp
{% endhighlight %}

---

## A Word on Future Direction of Template Parameter Pack Expansion

코드 작성 초기에 아래와 같이하고 CLion 1.2상에서 컴파일을 해보았다.
 
{% highlight cpp %}
template <typename F, typename... Args>
auto ForEachArgument(F f, Args... args)
{
   (f(args), ...);
   return f;
}
{% endhighlight %}

현재시점에서는 정상적으로 컴파일되나 다음과 같은 오류 메시지가 발생했다.
<pre>
<code>
warning: pack fold expression is a C++1z extension [-Wc++1z-extensions]
</code>
</pre>

해당 코드를 작성한 시점에 테스트를 작성하지 않아서 돌려보지는 않았었다. 차기표준(C++17)에서 이런 표현식을 지원한다나 그런 것 같다. 당장 사용은 불가능하겠다. parameter pack을 확장하는 방법이 좀더 유연해지나보다. (해석하는 방법도 그렇냐면, 글쎄,... 아직은...)

---

## A Word on Original for_each_element Function Template

아래 코드는 상단 비디오 링크에서 언급되는 Sean Parent가 트위터상에 트윗했다는 `for_each_element` 코드이다.

{% highlight cpp %}
template <class F, class... Args>
void for_each_element(F f, Args&&... args) {
    [](...){}((f(std::forward<Args>(args)), 0)...);
}
{% endhighlight %}

첫 느낌은 암호문이 따로없다. :-)

이해에 방해가되는 `std::forward` 부분을 없애버리면 구현부 코드는 이렇게 된다.

{% highlight cpp %}
    [](...){}((f(args), 0)...);
{% endhighlight %}

이 것은 두부분으로 나누어서 생각하면되겠다.

1. `[](...){}`: 가변인자를 받아서 아무것도 하지 않는 lambda 표현
1. `(f(args), 0)...`: 앞서 `std::initializer_list`를 사용한 버전에서 언급한 내용과 동일. parameter pack 확장. lambda 함수의 인자로 넘기게되는데, 이와 같은 '함수호출인자리스트' context에서 parameter pack 확장이 허용된다.

종합하면 parameter pack이 확장되서 `args`부분에 해당하는 인자 개수만큼 얻어지는 결과 `0`들의 값들을 아무것도 하지 않는 lambda에 전부 넘긴다는 것이다. 결과적으로 `std::initializer_list` 구현과정에서 언급한 `f`의 리턴값 문제와 컴파일러 경고 문제를 말끔히 해결한다.

실제로 이 코드를 이용한 테스트를 돌려보지는 않았다. (귀찮아져서... :-))

하지만, 이 버전의 코드는 문제가 있을 수 있는 상황이 있다. 함수호출인자로 전달되는 표현식들이 evaluation되는 순서가 표준에서 명시되어 있지 않다고 하는 것 같다. 그러니까, 개념적으로 어떤 컴파일러는 최우측 인자값부터 계산해 나갈 수도 있고 그 반대일 수도 있을 수 있다는 것이다. `for_each_argument`내부에서 lambda에 parameter로 받은 `f` functor를 함수호출인자리스트 context에서 호출하게되는데, 만약 `f`의 호출순서에 의해서 뭔가 결과값등이 달라질 수 있는 상황이라면 문제되겠다. 반면에 `braced initialization` context에서는 첫번째 원소에 대한 표현식부터 차례데로 evaluation되는 것이 보장된다고 한다.
 
---

## 참고
+ [Variadic Templates are Funadic](https://channel9.msdn.com/Events/GoingNative/GoingNative-2012/Variadic-Templates-are-Funadic): Andrei Alexandrescu가 진행하는 강연이다. Parameter pack expansion rule에 대해서 자세히 설명되어 있다.
+ [Using Variadic Templates cleanly](http://florianjw.de/en/variadic_templates.html): `std::initializer_list`와 variadic template을 적절히 활용하는 방법이 자세히 설명되어 있다. 맨 하단의 `sequential_foreach`는 거의 `ForEachArgument`와 동일하다.   
+ <https://github.com/ghjang/rocky/blob/master/rocky/base/ForEachArgument.h>
+ <https://github.com/ghjang/rocky/blob/master/rocky/test/base/ForEachArgumentTest.cpp>
+ <https://github.com/ghjang/rocky/blob/master/rocky/base/ForEachTuple.h>: `ForEachArgument`를 활용하여 구현한 한가지 예.
+ <https://github.com/ghjang/rocky/blob/master/rocky/test/base/ForEachTupleTest.cpp>
