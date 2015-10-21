---
layout: post
title: "[C++11] std::enable_if"
description: "std::enable_if에 대해서 알아본다."
category: Computer Programming
tags: [C++, C++11, C++ TMP]
---
{% include JB/setup %}

std::enalbe_if class tempalte은 (member) function 호출시 overload resolution 과정에 사용되는 overload resolution set에 제약을 가하는데 사용될 수 있다. 또한 class template specialization을 컴파일러가 선택하는 과정에서도 class template specialization set에 제약을 가하는 도구로써 사용된다. 적절히 사용될 경우 유용하게 사용될 수 있지만 적절하지 못한사용은 오히려 혼란을 가중시킬 수 있을 것 같다. 직접 사용할 일이 많지는 않을 것 같으나 개념을 알고 있어야할 것 같아서 적어본다. 하단의 '참고' 부분의 MSDN쪽의 링크글내에 설명된 가이드라인 참고할 것.

enable_if를 제대로 이해하기 위해서는 SFINAE(Substitution Failure Is Not An Error) 개념을 이해해야한다. enable_if는 SFINAE 사용의 한 예이다. 하단의 '참고' 부분의 Wikipedia 링크의 글이 간결하게 잘 설명하고 있다.

SFINAE를 간단하게 설명하면 'template관련 코드에서 특정 부분만을 보면 잘못된(ill-formed) 컴파일이 실패하는 코드이나, 대체 방안이 있을 경우 문제된 코드부분은 무시되고 정상적으로 컴파일된다.'라고 할 수 있겠다.

enable_if의 적절한 활용예제를 생각해내기 쉽지 않았다. (실제 유용성은 그다지 없어보이는) 작위적으로라도 만들어본 예제는 다음과 같다. 주어진 컨테이너의 카테고리에 따라서 컨테이너내의 중간 위치에 값을 리턴해주는 구현을 달리 해본 함수이다.
{% highlight cpp %}
template <
        typename Container,
        typename = std::enable_if_t<
                            std::is_same<
                                    std::random_access_iterator_tag,
                                    typename std::iterator_traits<typename Container::iterator>::iterator_category
                            >::value
                    >
>
auto medianValue(Container const& c)
{
    return c[c.size() / 2];
}

template <
        typename Container,
        typename = void,
        typename = std::enable_if_t<
                            std::is_base_of<
                                    std::input_iterator_tag,
                                    typename std::iterator_traits<typename Container::iterator>::iterator_category
                            >::value
                                && !std::is_same<
                                        std::random_access_iterator_tag,
                                        typename std::iterator_traits<typename Container::iterator>::iterator_category
                                    >::value
                    >
>
auto medianValue(Container const& c)
{
    auto iter = c.begin();
    std::advance(iter, c.size() / 2);
    return *iter;
}

// ...

using std::vector;
using std::list;
using std::cout;

vector<int> v = { 1, 2, 3, 4, 5 };
auto vRetVal = medianValue(v);
cout << "vRetVal: " << vRetVal << std::endl; // vRetVal: 3

list<char> l = { 'a', 'b', 'c', 'd', 'e' };
auto lRetVal = medianValue(l);
cout << "lRetVal: " << lRetVal << std::endl; // lRetVal: c
{% endhighlight %}
여기서 두 함수의 마지막 template parameter의 default argument 표현식은 주어진 컨테이너에 대해서 둘 중에 한곳에서만 유효한 표현식이 된다. 결과적으로 하나의 함수만을 overload resolution set에 포함시키게 된다. 예를 들어서 std::vector의 인스턴스가 인자로 넘어온다고 할 경우 두 함수의 signature는 다음과 같다고 볼 수 있다.
{% highlight cpp %}
// first function
template <
        typename Container,
        typename = void
>
auto medianValue(Container const& c)
...

// second function
template <
        typename Container,
        typename = 
>
auto medianValue(Container const& c)
...
{% endhighlight %}
두번째 함수 signature는 잘못된(ill-formed) C++ 코드이다. 해서 해당 코드 부분은 무시된다. 대안방법에 해당하는 첫번째 함수가 존재하기 때문에 정상적으로 컴파일된다. 만약 첫번째 함수마저도 없었다면 호출가능한 함수가 없다는 형태의 컴파일 오류가 발생할 것이다. 

추가로 한가지 짚고 넘어가 볼만한 부분은 두번째 overload된 함수의 signature 부분이다. 이부분을 다음과 같이 작성했다면 컴파일 오류가 발생한다.
{% highlight cpp %}
template <
        typename Container,
        typename = std::enable_if_t<
                            std::is_base_of<
                                    std::input_iterator_tag,
                                    typename std::iterator_traits<typename Container::iterator>::iterator_category
                            >::value
                                && !std::is_same<
                                        std::random_access_iterator_tag,
                                        typename std::iterator_traits<typename Container::iterator>::iterator_category
                                    >::value
                    >
>
auto medianValue(Container const& c)
...
{% endhighlight %}
overload된 2개의 medianValue function template의 두번째 template parameter의 default argument에서 redefination관련 내용의 컴파일 오류가 발생한다. function template의 default template argument는 function template의 signature에 포함되지 않는다. 여기서는 이를 해결하기 위해서 사용하지 않는 dummy template parameter를 넣어 보았다.

또다른 이 문제 해결법은 다음과 같이 될 수 있을 것이다.
{% highlight cpp %}
template <
        typename Container,
        std::enable_if_t<
                std::is_base_of<
                        std::input_iterator_tag,
                        typename std::iterator_traits<typename Container::iterator>::iterator_category
                >::value
                    && !std::is_same<
                            std::random_access_iterator_tag,
                            typename std::iterator_traits<typename Container::iterator>::iterator_category
                        >::value
        > ** = nullptr
>
auto medianValue(Container const& c)
...
{%표현의 endhighlight %}

위 표현은 RandomAccessIterator를 제공하지 않는 컨테이너에 대해서 다음과 같은 형태로 내부적으로 바뀔 것이다.
{% highlight cpp %}
template <
        typename Container,
        void ** = nullptr
>
auto medianValue(Container const& c)
...
{% endhighlight %}

template parameter가 아닌 function parameter를 이용해서도 문제 해결이 가능하다.
{% highlight cpp %}
template <typename Container>
auto medianValue(Container const& c, std::enable_if_t<
                                             std::is_base_of<
                                                     std::input_iterator_tag,
                                                     typename std::iterator_traits<typename Container::iterator>::iterator_category
                                             >::value
                                                 && !std::is_same<
                                                         std::random_access_iterator_tag,
                                                         typename std::iterator_traits<typename Container::iterator>::iterator_category
                                                     >::value
                                     > ** = nullptr
)
...
{% endhighlight %}
'void **'와 같이 '**'를 이용한 것은 별다른 이유라기 보다는 최대한 사용자의 실수를 방지하기 위한 것이다. 

---

### 참고
+ [cppreference std::enable_if](http://en.cppreference.com/w/cpp/types/enable_if)
+ [MSDN std::enable_if](https://msdn.microsoft.com/en-us/library/ee361639.aspx): enable_if의 잘못된 사용에 대한 설명이 cppreference쪽보다 좀더 자세하다.
+ [Wikipedia SFINAE](https://en.wikipedia.org/wiki/Substitution_failure_is_not_an_error)
+ [https://github.com/ghjang/personal_study/blob/master/cpp/enable_if/main.cpp](https://github.com/ghjang/personal_study/blob/master/cpp/enable_if/main.cpp)
