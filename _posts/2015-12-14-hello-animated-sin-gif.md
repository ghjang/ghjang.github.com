---
title: "Hello! Animated Sin GIF"
description: ""
category: Computer Programming
tags: [Wolfram Language, Functional Programming, Math, Animated GIF]
---

Wolfram Language에서 아래와 같은 Animation GIF를 만들어 보았다.

![Animated Sin Graph]({{ site.postsResBase }}/2015-12-14/hello_animated_sin_ani.gif?raw=true)

사용한 코드는 다음과 같다.

```mma
Export["hello_animated_sin_ani.gif", Join[Table[Plot[ Sin[x*n], {x, 0, 2 Pi}, ImageSize -> 400], {n, 1, 10, 0.1}], Table[Plot[ Sin[x*n], {x, 0, 2 Pi}, ImageSize -> 400], {n, 10, 1, -0.1}]], "DisplayDurations" -> {0.025}]
```

요즘 수학 그래프등 관련하여 Animation GIF파일을 공유하는 것을 자주볼 수 있다. Wolfram Language로 작성한 것인지는 모르겠다. 하여간 보다시피 얼마안되는 코드로 간단한 그래프의 경우는 애니메이션화 하는 것이 아주 쉽다. Wolfram Language가 Mathematica의 언어라서 이런 식의 작업이 단순한 건 당연한 일이기도 할 것이다.
