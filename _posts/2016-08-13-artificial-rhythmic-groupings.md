---
layout: post
title: "Artificial Rhythmic Groupings?"
description: "잇단음표에 대해서 알아본다."
category: Music Theory
tags: [Music, Music Theory]
---
{% include JB/setup %}

**Artificial Rhythmic Groupings**는 우리말로는 **잇단음표**로 번역되고 있나보다.

> _'응? 뭔 용어번역이 이러냐?'_

개인적으로는 이 영어용어를 처음 접하고나서는 우리말용어와 많이 달라서 좀 당황했다. 직역을 했다면 **'인위적 리듬 분할'** 정도의 의미가 되지 않았을까 한다.

> _'응? 인위적이라... 뭐가 인위적이라는 것이냐?'_

---

## Notes and Duration

**기보법(Musical Notation)**에서는 **4분음표(Quater Note)를 1박(Beat)으로 기준**을 정하고 나머지 음표들의 박의 길이를 **상대적**으로 따진다.

<br/>

|        | 음표 이름                | 박의 길이 비율   |
|--------|------------------------|--------------|
|        | 온음표(Whole Note)       | 4            |
|        | 2분음표(Half Note)       | 2            |
|&#x2669;| 4분음표(Quater Note)     | 1            |
|&#x266a;| 8분음표(Eighth Note)     | 1 / 2 = 0.5  |
|        | 16분음표(Sixteenth Note) | 1 / 4 = 0.25 |

<br/>

**점음표(Dotted Notes)**에 대해도 같은 방식으로 나타내보면 아래와 같다.

<br/>

| | 점음표 이름                        | 박의 길이 비율                             |
|-|---------------------------------|-----------------------------------------|
| | 점온음표(Dotted Whole Note)       | 4 + (4 / 2) = 6                         |
| |점2분음표(Dotted Half Note)        | 2 + (2 / 2) = 3                         |
| |점4분음표(Dotted Quater Note)      | 1 + (1 / 2) = 3 / 2 = 1.5               |
| |점8분음표(Dotted Eighth Note)      | (1 / 2) + ((1 / 2) / 2) = 3 / 4 = 0.75  |
| |점16분음표(Dotted Sixteenth Note)  | (1 / 4) + ((1 / 4) / 2) = 3 / 8 = 0.375 |

<br/>

32분음표 이상도 있겠으나 여기서는 나타내지 않는다.

---

## How Do I Notate 1/3 Duration of a Quater Note?

위의 테이블에 나열한 **박의 길이 비율**을 보면 **1/3(==0.333...)**, **1/6(==0.1666...)**, **1/5(==0.2)** 또는 **6/5(==1.2)** 같은 비율들을 나타내는 부분이 없다는 것을 알 수 있다.

**_이러한 비율의 길이(duration)을 나타내려면 어떻게 해야할 것인가?_**

1. 기존 기보법에 해당 비율에 해당하는 새로운 음표를 추가한다.
1. 기존 기보법에 특정 비율을 나타내는 액세서리 형태의 표기를 추가한다.
1. ...

뭐, 대략 이런 생각을 해볼 수 있을 것이다. 첫번째 방법을 선택하게되면 추가해야할 비율이 있을때마다 새로운 음표를 정의해야했을 것이다. 매우 귀찮기도하고 현실성이 없다. 두번째 방법이 실제 채택되어 사용되고 있는 방법이다.

예를 들어서 **'4분음표 1박을 1/3박씩 세등분'**하기 위해서는 다음과 같은 **셋잇단음표(triplet)** 표기를 사용한다는 것이다.

<img alt='Quater Note Triplet Score' style='width: 400px' src='{{ site.post_resource_file_base }}/2016-09-05/quater_note_triplet_score.png?raw=true'/>

4/4박자의 1마디에서 첫박은 4분음표로, 3번째박은 셋잇단음표로 표시한 것이다. 이 표기를 **MIDI노트** 표기로 나타낸 것이 다음 그림이다. 악보에서보다 훨씬 이부분에 대해서는 그 의미를 확실히 이해할 수 있게된다.

<img alt='Quater Note Triplet MIDI Note' style='width: 400px' src='{{ site.post_resource_file_base }}/2016-09-05/quater_note_triplet_piano_roll.png?raw=true' />

**'4분음표 1박을 1/5박씩 5등분'**해서 **다섯잇단음표(quintuplet)**로 악보에 표기하면 다음과 같이 된다. 참고로 위의 두 그림은 **Apple Logic Pro X** 상에서 화면을 캡쳐한 것이다. 아래는 **Noteflight**라는 웹사이트에서 음표를 입력한후에 캡쳐한 화면이다. Logic Pro X 상에서 다섯잇단음표 악보표기를 제대로 표시하지 못한다는 것을 나중에서야 알았다...

<img alt='Quater Note Quintuplet' style ='width: 400px' src='{{ site.post_resource_file_base }}/2016-09-05/quater_note_quintuplet_score.png?raw=true' />

마지막 예제로 **'4분음표 1박을 1/3박씩 세등분하고, 마지막 1/3박은 다시 이등분 그러니까 1/6박으로'** 쪼개서 표기하면 다음과 같다. 악보표기는 Noteflight에서 캡쳐한 것이고 MIDI노트 화면은 Logic Pro X에서 했다.

<img alt='Quater Note Triplet Score 1' style='width: 400px' src='{{ site.post_resource_file_base }}/2016-09-05/quater_note_triplet_score_1.png?raw=true' />

<img alt='Quater Note Triplet MIDI Note 1' style='width: 400px' src='{{ site.post_resource_file_base }}/2016-09-05/quater_note_triplet_piano_roll_2.png?raw=true' />

---
