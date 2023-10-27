---
title: "Getting catch.hpp's Test Case Name"
description: ""
category: Computer Programming
tags: [C++, Catch, Testing]
---

`catch.hpp`의 `TEST_CASE` 매크로를 사용해 작성한 테스트 케이스의 코드에서 **지정한 테스트 케이스의 이름 자체**를 얻어 오는 방법을 간단히 설명한다.

---

## Expanding the TEST_CASE Macro

다음과 같은 최소한의 내용을 갖춘 Catch 테스트 케이스가 있다고 하자:

```c++
#include "catch.hpp"

TEST_CASE("test_case_0", "[my_test]")
{
    CHECK(100 == 100);
}
```

최종적으로 하고자 하는 바는 다음처럼 이 테스트 케이스의 이름 **test_case_0**을 이 테스트 케이스 코드 자체에서 참조하는 것이다. 여기서는 `THIS_TEST_CASE_NAME` 부분이 이름 참조부분을 나타내는 것으로 보면 된다:

```c++
TEST_CASE("test_case_1", "[my_test]")
{
    INFO("name: " << THIS_TEST_CASE_NAME);
    CHECK(false);
}
```

우선 간단히 생각해 볼 수 있는 아이디어는 `TEST_CASE` 매크로를 *확장(expansion)*해 생성된 코드에서 원하는 테스트 케이스 이름 관련 정보를 얻을 수 있는지 살펴보는 것이다. 다음과 같이 컴파일러 커맨드를 실행하면 전처리 단계까지만 수행해줄 수 있다. 컴파일러마다 해당 옵션은 다를 수 있으니 검색을 하거나 매뉴얼을 참조하라:

```
g++ -P -E test_case.cpp
```

단순히 위와 같이 실행하면 콘솔에 전처리가 진행된, 그러니까 매크로가 모두 확장된 C++코드가 출력된다. 표준 헤더 파일들까지 모두 포함되기에 내용이 방대하고 콘솔에 출력되어 느리다. 다음과 같이 출력을 파일로 *리다이렉션*한 후 에디터에서 확인하는 것이 좋다:

```
g++ -P -E test_case.cpp > temp_out.cpp
```

예제로 제시한 테스트 케이스 부분의 출력 결과물은 다음과 같다:

```c++
static void ____C_A_T_C_H____T_E_S_T____0(); namespace{ Catch::AutoReg autoRegistrar1( &____C_A_T_C_H____T_E_S_T____0, ::Catch::SourceLineInfo( "test_case.cpp", static_cast<std::size_t>( 5 ) ), Catch::NameAndDesc( "test_case_0", "[my_test]" ) ); } static void ____C_A_T_C_H____T_E_S_T____0()
{
    do { Catch::ResultBuilder __catchResult( "CHECK", ::Catch::SourceLineInfo( "test_case.cpp", static_cast<std::size_t>( 7 ) ), "100 == 100", Catch::ResultDisposition::ContinueOnFailure ); try { ( __catchResult <= 100 == 100 ).endExpression(); } catch( ... ) { __catchResult.useActiveException( Catch::ResultDisposition::ContinueOnFailure ); } if( __catchResult.shouldDebugBreak() ) if( Catch::isDebuggerActive() ) { __asm__("int $3\n" : : ); }; __catchResult.react(); } while( Catch::isTrue( false && static_cast<bool>( !!(100 == 100) ) ) );
}
```

매크로에 대해 *수직풀기(vertical repeat)*기법이 아닌 보통의 *수평풀기(horizontal repeat)*를 사용하기에 코드가 매우 긴 몇개의 줄만으로 생성되었다. 사용하는 에디터의 코드 포맷팅 기능을 활용해 줄바꿈과 들여쓰기를 해주면 다음과 같은 모습이다:

> Visual Studio Code의 경우 해당 영역을 선택후 _Format Selection(Cmd + K Cmd + F)_ 커맨드를 실행해주면 된다.

```c++
static void ____C_A_T_C_H____T_E_S_T____0();

namespace
{
Catch::AutoReg autoRegistrar1(&____C_A_T_C_H____T_E_S_T____0, ::Catch::SourceLineInfo("test_case.cpp", static_cast<std::size_t>(5)), Catch::NameAndDesc("test_case_0", "[my_test]"));
}

static void ____C_A_T_C_H____T_E_S_T____0()
{
    do
    {
        Catch::ResultBuilder __catchResult("CHECK", ::Catch::SourceLineInfo("test_case.cpp", static_cast<std::size_t>(7)), "100 == 100", Catch::ResultDisposition::ContinueOnFailure);
        try
        {
            (__catchResult <= 100 == 100).endExpression();
        }
        catch (...)
        {
            __catchResult.useActiveException(Catch::ResultDisposition::ContinueOnFailure);
        }
        if (__catchResult.shouldDebugBreak())
            if (Catch::isDebuggerActive())
            {
                __asm__("int $3\n"
                        :
                        :);
            };
        __catchResult.react();
    } while (Catch::isTrue(false && static_cast<bool>(!!(100 == 100))));
}
```

얻고자 했던 테스트 케이스명 **test_case_0**이 실제 테스트가 수행되는 코드 블럭밖 *un-named namespace*의 `Catch::AutoReg` 객체 생성 인자 리스트에 있는 것을 볼 수 있다.

원래 naive하게 생각했던 것은 테스트 코드 블럭 내에 테스트 케이스 이름을 저장하는 (정적) 변수 같은게 있다면 간단한 사용자 정의 매크로를 추가해 해당 테스트 케이스 이름을 얻을 수 있지 않을까하는 것이었다. (당연히) 보기 좋게 예상은 빗나갔다.

참고로 `Catch::AutoReg` 코드를 따라가 보면 내부적으로 *Registry Hub*라고 부르는 객체를 통해서 정보를 저장 한다는 것을 알 수 있다. 해당 객체는 *싱글톤(Singleton) 패턴*으로 구현되있다.

---

## Creating a Listener for the Test Cases

테스트 코드 블럭에서 테스트 케이스 이름 자체를 끌고 올 방법이 마땅히 없다는 것을 확인 후 다시 매뉴얼을 좀 살펴보았다. 천천히 차례대로 읽던 중 *Listener*라고 지칭하는 섹션에서 정확히 얻고자 하는 정보를 구하는 데 필요한 설명을 하고 있었다. 뭐, 간단히 요약하자면 전체 테스트가 수행되는 과정에서 특정 시점의 이벤트 정보를 등록된 객체에 *콜백(callback)* 형태로 전달해 준다. 일종의 *옵저버(Observer) 패턴*인 것이다. 매뉴얼에서 제공하는 예제 코드를 사용해 다음과 같은 코드를 작성했다:

### catch_util.h

```c++
#ifndef CATCH_UTIL_H
#define CATCH_UTIL_H


#include <string>
#include "catch.hpp"


#if defined(CATCH_CONFIG_MAIN) || defined(CATCH_CONFIG_RUNNER)
    struct TestCaseNameRecorder final : Catch::TestEventListenerBase
    {
        using TestEventListenerBase::TestEventListenerBase; // inherit constructor

        void testCaseStarting(Catch::TestCaseInfo const& testInfo) override
        { TestCaseNameRecorder::testCaseName_ = testInfo.name; }
        
        void testCaseEnded(Catch::TestCaseStats const& /* testCaseStats */) override
        { TestCaseNameRecorder::testCaseName_.erase(); }

        static std::string testCaseName_;
    };

    std::string TestCaseNameRecorder::testCaseName_{};

    CATCH_REGISTER_LISTENER(TestCaseNameRecorder)

    std::string current_test_case_name()
    { return TestCaseNameRecorder::testCaseName_; }
#else
    std::string current_test_case_name();

#   define THIS_TEST_CASE_NAME current_test_case_name()
#endif


#endif // CATCH_UTIL_H
```

`TestCaseNameRecorder` 코드는 전처리 제어를 통해 `CATCH_CONFIG_MAIN`나 `CATCH_CONFIG_RUNNER`가 정의되어 있을 경우에만 포함되게 했다. 그 이유는 Catch가 `Catch::TestEventListenerBase`의 정의를 이 매크로 정의가 있을 경우에만 포함하게 되어 있기 때문이다.

Catch 매뉴얼에 따르면 Catch는 테스트 케이스를 병렬로 실행하지 않고 순차적으로 실행한다. 따라서 이 코드 처럼 실행되는 테스트 케이스의 이름을 정적 변수에 별다른 동기화 처리 없이 저장하는 것이 문제되지 않는다.

> 참고로  Catch에서 테스트 케이스를 병렬로 실행하려면 테스트 케이스를 분할해 여러개의 실행파일을 만든 후 각 실행파일을 한꺼번에 실행하거나, 모든 테스트 케이스를 하나의 실행파일에 구현 후 테스트 실행시 커맨드 인자를 달리해 여러번 실행해주어야 한다.

작성한 `catch_util.h`는 다음과 같이 사용하면 된다. 우선 Catch의 *main*이 위치하게 될 cpp파일을 다음처럼 작성해 준다:

```c++
#define CATCH_CONFIG_MAIN
#include "catch.hpp"

#include "catch_util.h"
```

그러고 나서 테스트 케이스가 있는 cpp파일에서 다음과 같이 `THIS_TEST_CASE_NAME` 매크로를 사용해 현재 실행 중이 코드 블럭이 속한 테스트 케이스의 이름을 얻어 올 수 있다:

```c++
#include "catch.hpp"
#include "catch_util.h"


TEST_CASE("test_case_0", "[my_test]")
{
    INFO("name: " << THIS_TEST_CASE_NAME);
    CHECK(false);
}
```

여기서 `CHECK(false)`를 사용한 것은 `INFO`로 출력한 내용은 테스트 케이스가 정상적으로 수행된 상황에서는 내부에 기록만되지 실제 출력은 되지 않기 때문에 고의로 테스트 케이스가 실패하게 만든 것이다.

---

## What Is It Good for?

생각하기에 따라서 여러가지 활용법이 있을 수 있다.

간단하게는 각 테스트 케이스에서 테스트 케이스 이름 기반으로 중간 결과를 저장해주고 최종적으로 관련 있는 중간 결과를 취합해 어떤 또다른 테스트를 수행할 수도 있을 것이다.

`CATCH_CONFIG_MAIN` 대신에 `CATCH_CONFIG_RUNNER`를 사용해 전체 테스트를 전부 수행후 종료 직전에 어떤 의미있는 자료를 (외부 스크립트 코드가 아닌 C++ 코드로) 만들 수도 있을 것이다.

(한 cpp파일에서 `TEST_CASE` 매크로로 생성한 코드가 테스트 케이스를 등록하는 방법에 해당 cpp 파일에 격리된 전역 객체를 사용하고, 한 cpp파일내에서는 객체의 생성 순서가 보장되므로 테스트 케이스 등록 순서를 그 cpp파일에 제한적이지만 알 수 있다. 따라서 자세히 Catch 구현 코드를 확인해보지 않았지만 (아마도) 테스트 케이스가 등록된 순서대로 실행된다면 이에 따른 어떤 작업을 해줄 수 있을지도 모르겠다. Catch에서 테스트에 추가적으로 지정한 tag로 정렬등을 수행후 테스트를 진행한다면 이런 식의 작업은 위험(?)한 생각이다. 된다고 하더라도 추후 구현이 어떻게 바뀔지도 모르고 이런 내부의 구현을 직접적으로 건드리는 것은 그리 바람직하지 않겠다.)

---

## References

+ <https://github.com/philsquared/Catch>: Catch C++ 테스트 프레임워크
+ <https://github.com/ghjang/personal_study/tree/master/cpp/catch>: 간단한 Catch 리스너 테스트 코드를 포함함.
