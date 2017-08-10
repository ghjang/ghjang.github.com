---
title: "Building Prototype clang for Metaclass"
description: ""
category: Computer Programming
tags: [C++]
---

최근 *Herb Sutter*의 **Mataclass**관련 *Accu* 동영상을 보고 좀 놀랬다.

> *"C++ 언어의 진화는 어디까지 계속될 것인가?"*

메타클래스가 무엇인지에 대해서는 다음 동영상과 글을 참고하라:

* [Thoughts on Metaclasses - Herb Sutter - Keynote [ACCU 2017]](https://youtu.be/6nsyX37nsRs)
* [Metaclasses: Thoughts on generative C++](https://herbsutter.com/2017/07/26/metaclasses-thoughts-on-generative-c/)
* [Current Metaclasses proposal paper: P0707R1](https://herbsutter.files.wordpress.com/2017/07/p0707r1.pdf)

여기서는 메타클래스 자체를 설명하는 것이 아니라, 위의 동영상과 글의 설명에 사용된 예제 코드를 실제로 컴파일해볼 수 있는 *프로토타입 clang*을 직접 빌드하는 것을 다룬다. 빌드후에 간단한 메타클래스 예제 코드를 컴파일하는 방법까지 적어 보겠다. 물론 위의 동영상과 글에서 설명하는 사용가능한 온라인 컴파일러를 이용할 수도 있다. 하지만 좀더 많은 테스트를 해보고자 한다면 로컬에서 직접 clang을 빌드해서 테스트를 하는 것이 여러면에서 편리할 것이다.

> NOTE: 미리 빌드된 Linux용 프로토타입 clang 바이너리는 GitHub 레포지토리에 다운로드 링크가 있다: <https://github.com/asutton/clang/releases/download/0.1.0/clang-cppx.tar.xz>

---

## Building clang

빌드는 macOS 환경에서 clang 4.0.0을 사용했다. 다음 순서로 진행하면 된다:

### 1. Making a Work Folder

커맨드라인을 실행하고 clang 빌드 작업용 폴더를 생성후 그 폴더로 이동 한다.

```bash
> mkdir llvm_clang_build
> cd llvm_clang_build
```

### 2. LLVM 5.0.0 checkout

메타클래스를 지원하는 프로토타입 컴파일러는 clang 5.0.0 기반이어서 LLVM 5.0.0이 필요하다.

```bash
> svn co -r 304100 http://llvm.org/svn/llvm-project/llvm/branches/release_50 llvm
```

> 리비전 *304100*을 사용하는 이유는 현재시점(2017/08/10)에서 프로토타입 컴파일러 git 소스 레포지토리 마스터의 소스코드를 정상적으로 컴파일 성공시키는 포인트가 대략 이 리비전이기 때문이다. LLVM 5.0.0 브랜치의 최신 소스를 사용하면 인터페이스 변경으로 인해서 빌드가 실패한다. 또한 너무 이전의 버전을 선택해도 마찬가지다. 프로토타입 컴파일러가 사용한 LLVM의 정확한 버전 정보는 (못찾은 것일 수도 있지만) 없기 때문에 여러가지 조합을 시도해서 정상빌드되는 리비전을 얻었다. 메타클래스에 대한 테스트를 진행해보자하는 것이 목적이기에 clang의 모든 기능이 완전하게 오류없이 동작할 필요는 없다.

### 3. Prototype clang 5.0.0 clone

clang은 LLVM의 한 tool이다. tools 폴더 하위에 클론해주어 LLVM 전체 빌드과정에서 clang이 빌드될 수 있게 해주어야 한다.

```bash
> cd llvm/tools
> git clone https://github.com/asutton/clang.git
```

> NOTE: 마스터에서 클론하고 있다. 2017/08/10 지나서 클론하고 소스 변경이 마스터에 있었을 경우 LLVM과의 인터페이스 불일치 문제로 빌드가 실패할 수도 있다.

### 4. Build

실제 빌드 중간 결과들이 생성될 폴더를 생성하고 이동해준다.

```bash
> cd ../..
> mkdir build
> cd build
```

다음과 같이 *CMake*를 사용해 빌드 설정을 해준다.

```bash
> cmake -G "Unix Makefiles" ../llvm -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/Users/gilhojang/clang/llvm_clang_cppx -DCMAKE_C_COMPILER=/Users/gilhojang/clang/clang+llvm-4.0.0-x86_64-apple-darwin/bin/clang -DCMAKE_CXX_COMPILER=/Users/gilhojang/clang/clang+llvm-4.0.0-x86_64-apple-darwin/bin/clang++ -DCMAKE_CXX_FLAGS="${CMAKE_CXX_FLAGS} -std=c++1z"
> make
```

*CMAKE_BUILD_TYPE*을 설정하지 않으면 기본값은 *Debug*라고 한다. *CMAKE_INSTALL_PREFIX*를 설정해 최종 빌드 결과를 설치할 폴더를 지정해줄 수도 있다. 이 커맨드는 *Release*로 빌드하고 설치를 */Users/gilhojang/clang/llvm_clang_cppx* 위치에 하고 로컬에 받아 놓은 clang 4.0.0 컴파일러를 이용해서 빌드 수행하는 예이다.

(*make*를 실행하여 빌드를 시작하면 시스템에 따라 다르겠지만, 대략 2시간이 넘게 걸려 빌드가 성공할 것이다.)

빌드 완료후 최종 결과를 *CMAKE_INSTALL_PREFIX*에 지정한 경로에 설치하기 위해 다음을 실행해준다.

```bash
> make install
```
---

---

## Conclusion

메타클래스는 아마 별다른 문제가 없다면 *C++20*이나 *C++23* 즈음에 추가되지 않을까 싶다. 새로운 *Concept*, *Compile-time Reflection*, *Compile-time Programming* 언어 피쳐 기반으로 동작하는 메타클래스는 C++ 코딩하는 스타일을 또다시 생각해보게 만든다. 클래스와 관련된 특정 피쳐를 언어 스펙이 아닌 코드 작성자가 직접 컴파일 타임에 정의할 수 있게한다는 개념은 (아직은) 놀랍기만 하다. 메타프로그래밍에 대한 생각도 다시 해보아야할 것이다. 뭔가 머리가 여러가지 상상들로 복잡해졌지만, 왠지 굉장히 흥미진진해졌다. ~ :)
