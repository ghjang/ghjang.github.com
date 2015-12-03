---
layout: page
title: "C++ Study Resources"
description: "C++ 공부에 유용한 리소스들을 간략하게 정리한다."
---
{% include JB/setup %}

## C++ Standard
+ [https://isocpp.org](https://isocpp.org)
+ [http://cppreference.com](http://cppreference.com)
+ [SGI Standard Template Library Programmer's Guide](https://www.sgi.com/tech/stl/): 좀 오래된 사이트. C++11과 같은 근래의 표준이 나오기 전에 만들어진 사이트임. 하지만 내용물이 깔끔하고 여전히 유용하다.

---

## Podcast

### [CppCast](http://cppcast.com)
주단위로 업데이트되는 English podcast. 일주일간 있었던 C++ 관련 최신 뉴스를 먼저 간략하게 리뷰한 후에 초대된 C++ 전문가와 특정 주제에 대해서 이야기하는 방식임. 

---

## Developer Conference

### CppCon
2014년부터 시작된 것으로 보임. 년 1회 진행되는 대규모 개발자 컨퍼런스. 최신 트렌드등에 대해서 알고 싶다면 필히 찾아서 봐야할 것이다. 내용이 방대하다.

+ 프로그램 강좌 - 
+ 비디오 강좌 - 
+ 강좌 자료 - 

### C++ Now

### C++ Going Native

### BoostCon

### Meeting C++

---

## C++ Experts

### Herb Sutter
+ [Sutter's Mill](http://herbsutter.com)

### Andrei Alexandrescu
+ [http://erdani.com](http://erdani.com) 

### Scott Meyers
+ [THE VIEW FROM ARISTEIA: SCOTT MEYERS' PROFESSIONAL ACTIVITIES AND INTERESTS](http://scottmeyers.blogspot.kr) 

### Dave Abrahams
+ [http://daveabrahams.com](http://daveabrahams.com): personal web page
+ [https://twitter.com/DaveAbrahams](https://twitter.com/DaveAbrahams): Twitter

### Eric Niebler
+ [http://ericniebler.com](http://ericniebler.com)

### Peter Dimov
+ [http://pdimov.com](http://pdimov.com)

---

## Article and Lecture

### [WikiDocs C++ Template Note](https://wikidocs.net/book/54)
[C++ Templates - The Complete Guide](http://www.josuttis.com/tmplbook/) 책을 기본으로하고 다른 C++ 명저들을 공부하면서 얻은 내용들이 함께 정리된 곳. 나름 깔끔하게 정리되어 있어서 좋다. 한글 내용이다. 한글이다보니 용어 설명이 와닿지 않고 혼동될 수 있는 부분들이 있다. 가능하면 영문과 같이 익히는게 좋겠다.

### [What Does Haskell Have to Do with C++?](http://bartoszmilewski.com/2009/10/21/what-does-haskell-have-to-do-with-c/)
C++ Template Metaprogramming(C++ TMP) 시작용으로도 괜찮은 글이다.

C++ TMP를 ‘maximally obfuscated (subset of) Haskell’이라고 생각하라고 글 초반에 언급하고 있다. run-time Haskell 예제코드가 compile-time C++ TMP 코드로 어떻게 매핑이 되는지를 보이는 식으로 설명하고 있다. ‘fold, list comprehension, map, continuation’등과 같은 유용한 개념도 적절히 설명하고 있다. :-)

기존의 imperative programming 방식에만 익숙한 C++ 프로그래머는 당연히 C++ 언어의 functional programming 측면인 C++ TMP가 어렵게 느껴지게된다. 익숙해질려면 시간이 걸리겠다.

C++ TMP에 잘 적응되지 않게 방해하는 요소중 하나는 번잡한 template 사용문법이다. 글 마무리 부분에서도 언급되는데, C++ TMP가 이렇게 번잡한 것은 C++ 언어설계시에 TMP가 고려된 것이 아니라, 나중에 ‘발견’된 탓이다. C++ template은 참 강력함은 물론이거니와 오묘하다… :-)

### [Haskellizing C++ template metaprogramming](http://manu343726.github.io/c++/haskellizing-tmp/)

### [Variadic Templates are Funadic](https://channel9.msdn.com/Events/GoingNative/GoingNative-2012/Variadic-Templates-are-Funadic)
Andrei Alexandrescu가 진행하는 C++ Going Native 강연이다. Parameter pack expansion rule에 대해서 자세히 설명되어 있다.

### [Using Variadic Templates cleanly](http://florianjw.de/en/variadic_templates.html)
`std::initializer_list`와 variadic template을 적절히 활용하는 방법이 자세히 설명되어 있다.

---

## C++ IDE

### [Microsoft Visual Studio Community Edition](https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx)
마이크로소프트에서 제공하는 무료버전의 Visual Studio

### [JetBrains CLion](https://www.jetbrains.com/clion/)
Cross-platform C++ IDE. 오픈소스 프로젝트, 교육기관등에서 사용할 경우는 무료이나 개인용으로 이용할 경우에는 비용을 지불해야한다.

### [Cevelop](https://www.cevelop.com)
Eclipse 기반의 Cross-platform C++ IDE. 무료.

### Online C++ Compiler
웹상에서 코드를 편집하고 실행후 결과까지 확인할 수 있는 서비스를 제공하는 사이트들이 있다. 몇개 나열해본다.

+ [Online Visual C++ Compiler](http://webcompiler.cloudapp.net)
+ [gcc.godbolt.org (Clang, GCC, Intel ICC)](http://gcc.godbolt.org)
+ [C++ shell](http://cpp.sh)
+ [Wandbox  (Clang, gcc – includes Boost)](http://melpon.org/wandbox/)
