---
title: "Tower of Hanoi"
description: ""
category: Computer Programming
tags: [C++, C++ TMP, Algorithm]
---

**C++ Template Metaprogramming**에서 **recursion**의 이해는 필수사항이다. **Hanoi Tower**는 이 recursion과 관련하여 생각해볼만한 문제이다. Hanoi Tower 문제풀이는 결과코드만 보면 단순하다. 하지만 recursion에 익숙하지 않은 경우 (해답을 보지 않고) 결과에 다다르는 생각을 해내는 것이 쉽지만은 않을 수도 있다. 이런 방식의 사고방식을 좀 고민해볼 필요가 있다.

```cpp
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
```

판의 세개 위치점을 각각 `0`, `1`, `2` 정수 인덱스 값으로 표현하고 있다. 탑의 높이가 `5`일 경우 맨위의 제일 작은 판의 인덱스는 `1`이고 맨 아래의 제일 큰 판의 인덱스는 `5`이다. 다음과 같은 식의 사고방식이다.

1. 최초 이동 대상이되는 n번째 판의 위에 있는 'n - 1' 판(들)을 n번째 판의 최종 이동 위치가 아닌 다른 위치로 먼저 이동
1. n번째 판을 최종 위치로 이동  
1. 'n - 1' 판(들)을 n번째 판이 있는 위치로 이동

탑의 높이가 5일 경우 위 프로그램의 실행결과는 다음과 같다.

```
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
```

여기서 `plate-1, (0 => 2)`와 같은 표현은 '1번 판을 0에서 2의 위치로 이동'을 의미한다. 타워를 렌더링하는 부분을 작성한다면 좀더 확인이 쉽기는 하겠으나,... 일단 패쓰. 종이를 잘라서 1 ~ 5까지 높이에 대해서 출력된 결과를 따라서 해보니 정확히 맞는다.

---

## Compile-time Tower of Hanoi

탑의 높이가 정해지면 전체 이동 경로 시퀀스는 정해져있다. 따라서 이동 경로 시퀀스 계산을 compile-time으로 끌어내릴 수 있다. 물론 이 방식은 탑의 높이를 사용자의 입력으로부터 동적으로 받아 처리하는 상황에서는 사용할 수 없다.

아래는 runtime 버전 코드의 개념을 그데로 compile-time으로 옮겨본 것이다.

compile-time MoveTower metafunction을 호출하고 이로부터 계산한 경로를 콘솔에 출력하는 코드를 먼저 적어본다.

```cpp
using tower_movement_sequence_t = typename MoveTower<
                                                int_c_t<towerHeight>,
                                                int_c_t<0>,
                                                int_c_t<2>,
                                                int_c_t<1>
                                            >::type;
PrintTowerMovementSequence(tower_movement_sequence_t());
```

코딩 편의를 위해서 `int_c_t` type alias를 추가했다.

```cpp
template <int i>
using int_c_t = std::integral_constant<int, i>;
```

특정 plate의 이동값을 나타내는 type alias를 추가했다. 여기서 `NthPlate`, `FromIndex`, `ToIndex`는 `std::integral_constant`의 특정 instance이다.

```cpp
template <typename NthPlate, typename FromIndex, typename ToIndex>
using plate_movement_t = std::tuple<NthPlate, FromIndex, ToIndex>;
```

'이동값 type'들을 담을 'type container'용 type alias를 추가했다. 

```cpp
template <typename... Sequence>
struct TowerMovementSequence
{ };
```

이동값 type들의 sequence를 병합하기위한 도우미성 metafunction을 추가했다.

```cpp
template <typename TowerMovementSequence, typename... Movement>
struct JoinTowerMovementSequence;

template <typename TowerMovementSequence, typename Head, typename... Tail>
struct JoinTowerMovementSequence<TowerMovementSequence, Head, Tail...>
{
    using type = typename JoinTowerMovementSequence<
                                typename JoinTowerMovementSequence<TowerMovementSequence, Head>::type,
                                Tail...
                            >::type;
};

template <typename... Sequence, typename NthPlate, typename FromIndex, typename ToIndex>
struct JoinTowerMovementSequence
        <
            TowerMovementSequence<Sequence...>,
            plate_movement_t<NthPlate, FromIndex, ToIndex>
        >
{
    using type = TowerMovementSequence<Sequence..., plate_movement_t<NthPlate, FromIndex, ToIndex>>;
};

template <typename... Sequence1, typename... Sequence2>
struct JoinTowerMovementSequence
        <
            TowerMovementSequence<Sequence1...>,
            TowerMovementSequence<Sequence2...>
        >
{
    using type = TowerMovementSequence<Sequence1..., Sequence2...>;
};
```

실제 이동 시퀀스를 계산하는 metafunction을 추가했다. template 문법이 번잡해보이기는 하겠지만 runtime 버전의 내용과 개념이 동일하다.

```cpp
template <typename NthPlateIndex, typename FromIndex, typename ToIndex, typename RemainingIndex>
struct MoveTower
{
    using upper_plate_movement_sequence_1_t = typename MoveTower<
                                                            int_c_t<NthPlateIndex::value - 1>,
                                                            FromIndex,
                                                            RemainingIndex,
                                                            ToIndex
                                                        >::type;
    using current_plate_movement_t = plate_movement_t<
                                            NthPlateIndex,
                                            FromIndex,
                                            ToIndex
                                        >;
    using upper_plate_movement_sequence_2_t = typename MoveTower<
                                                            int_c_t<NthPlateIndex::value - 1>,
                                                            RemainingIndex,
                                                            ToIndex,
                                                            FromIndex
                                                        >::type;
    using type = typename JoinTowerMovementSequence<
                                upper_plate_movement_sequence_1_t,
                                current_plate_movement_t,
                                upper_plate_movement_sequence_2_t
                            >::type;
};

template <typename FromIndex, typename ToIndex, typename RemainingIndex>
struct MoveTower<int_c_t<1>, FromIndex, ToIndex, RemainingIndex>
{
    using type = TowerMovementSequence<plate_movement_t<int_c_t<1>, FromIndex, ToIndex>>;
};
```

계산된 compile-time 이동 시퀀스의 값을 콘솔에 출력해주는 도우미성 함수를 추가했다.

```cpp
template <typename PlateMovement>
void PrintTowerMovementSequence(TowerMovementSequence<PlateMovement>)
{
    std::cout << "plate-" << std::tuple_element_t<0, PlateMovement>::value << ","
                << " (" << std::tuple_element_t<1, PlateMovement>::value
                << " => " << std::tuple_element_t<2, PlateMovement>::value << ")"
                << std::endl;
}

template <typename PlateMovement, typename Head, typename... Tail>
void PrintTowerMovementSequence(TowerMovementSequence<PlateMovement, Head, Tail...>)
{
    std::cout << "plate-" << std::tuple_element_t<0, PlateMovement>::value << ","
                << " (" << std::tuple_element_t<1, PlateMovement>::value
                << " => " << std::tuple_element_t<2, PlateMovement>::value << ")"
                << std::endl;
    PrintTowerMovementSequence(TowerMovementSequence<Head, Tail...>());
}
``` 

출력한 결과를 확인하여 정상적인 이동경로를 계산한 것을 확인할 수 있다.

---

## 참고

+ <https://github.com/ghjang/personal_study/blob/master/cpp/hanoi_tower/main.cpp>
