---
layout: post
title: "Things to Remember when using C++ Template"
description: "쉽게 잊어버리는, 하지만 기억해야할만한 사항들에 대해서 정리한다."
category: Computer Programming
tags: [C++, C++ TMP]
---
{% include JB/setup %}

**Template argument deduction시에는 automatic type conversion은 고려되지 않는다.**

**Overload resolution시에 rvalue에 대해서 T와 T const&는 동일한 레벨의 perfect match로 취급된다.**

**Overload resolution시에 lvalue에 대해서 T와 T &는 동일한 레벨의 perfect match로 취급된다.**

**Class template내에 정의된 overload된 assignment operator는 default assignment operator를 대체하지 않는다.**

**인자순서, perfect match**
