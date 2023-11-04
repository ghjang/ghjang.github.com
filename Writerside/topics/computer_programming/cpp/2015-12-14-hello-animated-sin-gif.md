# Hello! Animated Sin GIF

**Wolfram Language**에서 아래와 같은 Animation GIF를 만들어 보았다.

![Animated Sin Graph](hello_animated_sin_ani.gif)

사용한 코드는 다음과 같다.

```
Export["hello_animated_sin_ani.gif", Join[Table[Plot[ Sin[x*n], {x, 0, 2 Pi}, ImageSize -> 400], {n, 1, 10, 0.1}], Table[Plot[ Sin[x*n], {x, 0, 2 Pi}, ImageSize -> 400], {n, 10, 1, -0.1}]], "DisplayDurations" -> {0.025}]
```

요즘 수학 그래프 관련하여 Animation GIF파일을 SNS상에 공유하는 것을 자주볼 수 있다. Wolfram Language로 작성한 것인지는 모르겠다. 하여간 보다시피 얼마안되는 코드로 애니메이션화 하는 것이 아주 쉽다. Wolfram Language가 **Mathematica**의 언어라서 이런 식의 작업이 단순한 건 당연한 일이겠다.
