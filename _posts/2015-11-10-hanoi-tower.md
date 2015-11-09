---
layout: post
title: "Tower of Hanoi"
description: ""
category: Computer Programming
tags: [C++, C++ TMP, Algorithm]
---
{% include JB/setup %}

C++ Template Meta-programming에서 recursion의 이해는 필수사항이다. Hanoi Tower는 이 recursion과 관련하여 생각해볼만한 문제이다. Hanoi Tower 문제는 결과코드만 보면 단순하다. 하지만, (해답을 보지 않고) 결과에 다다르는 생각을 해내는 것이 쉽지만은 않을 수도 있다. 이런 방식의 사고방식을 좀 고민해볼 필요가 있겠다.

{% highlight cpp %}
void move_tower(int nthPlate, int fromIndex, int toIndex, int remainingIndex)
{
    if (1 == nthPlate) { // if top plate,
        std::cout << "plate-" << nthPlate << ", (" << fromIndex << " => " << toIndex << ")" << std::endl;
        return;
    }
    move_tower(nthPlate - 1, fromIndex, remainingIndex, toIndex);
    std::cout << "plate-" << nthPlate << ", (" << fromIndex << " => " << toIndex << ")" << std::endl;
    move_tower(nthPlate - 1, remainingIndex, toIndex, fromIndex);
}

//...

constexpr int towerHeight = 5;

move_tower(towerHeight, 0, 2, 1);
{% endhighlight %}

세개의 위치점을 각각 '0', '1', '2' 정수 인덱스 값으로 표현하고 있다. 탑의 높이가 5일 경우 맨위의 제일 작은 판의 인덱스는 1이고 맨 아래의 제일 큰 판의 인덱스는 5이다. 다음과 같은 식의 사고방식이다.

1. 최초 이동 대상이되는 n번째 판의 위의 있는 'n - 1' 판(들)을 n번째 판의 최종 이동 위치가 아닌 다른 위치로 먼저 이동
1. n번째 판을 최종 위치로 이동  
1. 'n - 1' 판(들)을 n번째 판이 있는 위치로 이동

탑의 높이가 5일 경우 위 프로그램의 실행결과는 다음과 같다.
<code>
<pre>
plate-1, (0 => 2)
plate-2, (0 => 1)
plate-1, (2 => 1)
plate-3, (0 => 2)
plate-1, (1 => 0)
plate-2, (1 => 2)
plate-1, (0 => 2)
plate-4, (0 => 1)
plate-1, (2 => 1)
plate-2, (2 => 0)
plate-1, (1 => 0)
plate-3, (2 => 1)
plate-1, (0 => 2)
plate-2, (0 => 1)
plate-1, (2 => 1)
plate-5, (0 => 2)
plate-1, (1 => 0)
plate-2, (1 => 2)
plate-1, (0 => 2)
plate-3, (1 => 0)
plate-1, (2 => 1)
plate-2, (2 => 0)
plate-1, (1 => 0)
plate-4, (1 => 2)
plate-1, (0 => 2)
plate-2, (0 => 1)
plate-1, (2 => 1)
plate-3, (0 => 2)
plate-1, (1 => 0)
plate-2, (1 => 2)
plate-1, (0 => 2)
</pre>
</code>

'plate-1, (0 => 2)'와 같은 표현은 '1번 판을 0에서 2의 위치로 이동'을 의미한다. 타워를 렌더링하는 부분을 작성한다면 좀더 확인이 쉽기는 하겠으나,... 일단 패쓰. 종이를 잘라서 1 ~ 5까지 높이에 대해서 출력된 결과를 따라서 해보니 정확히 맞는다.

---

## Compile-time Hanoi Tower

TODO: 탑의 높이가 정해지면 이동 경로는 정해져있다고 볼 수 있을 것 같다. 따라서, 이동 경로 시퀀스를 compile-time을로 끌어내릴 수 있을 것도 같다. 이렇게 하는 것이 가능한지 좀더 생각해보고 가능하다면 구현까지 해보도록 할 것.

---

## 참고
+ [https://github.com/ghjang/personal_study/blob/master/cpp/hanoi_tower/main.cpp](https://github.com/ghjang/personal_study/blob/master/cpp/hanoi_tower/main.cpp)
