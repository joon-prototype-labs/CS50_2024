### References
- https://learning.edx.org/course/course-v1:HarvardX+CS50+X/block-v1:HarvardX+CS50+X+type@sequential+block@71a958764ce040ddb6ea30553bdc7ed1/block-v1:HarvardX+CS50+X+type@vertical+block@af6e917ee267462e8bf2f026e5d2d9e0
- https://cs50.harvard.edu/x/2024/notes/0/

### Note

#### 정리

- 컴퓨터의 모든것은 사람이 만들어 둔 것이다. 블랙박스로 가려졌을 뿐, 마법이 아니다.
- 컴퓨터에게 모든 것은 bit로 표현된다. 
- 사람이 이해하기 위해서 특정 bit를 특정 표현으로 매핑하여 사용한다.
- 문제를 해결하는 방법은 알고리즘이며, 좋은 알고리즘을 사용하는 것을 배워야 한다.
- 프로그래밍을 통해 알고리즘을 기계에게 명령할 수 있다.

#### 컴퓨터 과학의 모든 것

기본적으로 컴퓨터 프로그래밍은 입력을 받고 출력을 만드는 것, 즉 문제를 해결하는 것이다.    
입력과 출력 사이에서 일어나는 일, 즉 블랙박스 라고 부를 수 있는 일을 우리가 알아볼 것이다.

#### 단항법과 이진법

우리는 단항법(Unary Notation)을 사용해서 한 손가락이 하나의 수(base-1)를 나타낸다. 총 5개까지 셀 수 있다.

그러나 손가락 피거나 접은 상태, 즉 한 손가락이 2가지 상태(base-2)를 가진다면 한 손으로 0~31(총 32개, 2^5)의 수를 셀 수 있다.
한 자리수가 2가지 상태를 가지므로 이진법(Binary Notation)이라고 한다. (한 자리수가 N개의 자리를 가지면 N진법이다.)

#### 컴퓨터는 비트를 사용한다.

컴퓨터는 이진법을 사용한다. 
전기가 흐르는 상태를 1, 흐르지 않는 상태를 0으로 표현한다.
이를 bit라고 표현하는데, binary digit에서 유래되었다.

컴퓨터가 다루는 모든 글자, 이미지, 동영상, 무선 통신 등도 bit로 구성되는것이다.

#### 관행(컨벤션, 규격 등)

컴퓨터에는 여러 관행이 있다. (or 표준)

그러나 **이는 근본적으로 bit이며, 사용하는 문맥에 따라서 다르게 해석될 뿐이다.**

8개의 bit를 byte라고 부르거나, ASCII, Unicode, RGB 등이 해당된다.

컴퓨터가 0과 1만 가지고 있다면, 어떻게 글자를 표현할 수 있을까?

특정 숫자를 문자와 매핑시키는 규칙을 만들고, 모든 컴퓨터가 이를 지키면 된다.

[ASCII](https://en.wikipedia.org/wiki/ASCII)는 7bit로 알파벳와 숫자, 그 외 특수 기호와 컴퓨터에게 특별한 제어 코드를 가진다.

더 많은 글자를 표현하기 위해 Unicode 표준이 등장했다.    
더 많은 비트를 사용하지만, 더 많은 수를 표현할 수 있다.

`U+1F44D` 와 같이 표현하는데, `U+`는 Unicode임을 표현하는 표기(컨벤션)이고, 뒤 글자는 16진수 수이다. 

Unicode의 이모지는 개인화된 확장성을 위해서 설계되었다. (피부톤 추가, 이모지 조합 등)

(추가로, Unicode는 문자를 표현하는 표준이고, UTF-8, UTF-16 등은 인코딩 방식으로, 컴퓨터에 저장되거나 전송할 때 사용되는 방식이다.)

RGB 표기법 또한, 각 Red, Green, Blue를 1byte 범위의 밝기를 합쳐서 많은 색을 표현할 수 있다.

이러한 byte, ASCII, Unicode, RGB 나아가 동영상, 음악 등도 결국 bit로 표현될 뿐이다.

#### 알고리증

문제 해결은 컴퓨터 과학과 컴퓨터 프로그래밍의 핵심이다.

문제를 해결하기 위한 접근 방식을 알고리즘이라고 한다.

좋은 알고리즘은 다른 알고리즘보다 문제를 더 빨리, 적은 자원으로 해결할 수 있다.

#### 추상화

추상화는 문제를 점점 더 작은 문제로 단순화하는 행위이다.

우리는 추상화를 통해서 구체적이지만 불필요한 정보를 블랙박스 속으로 숨기고, 편리하게 사용할 수 있다.       
(이를 통해서 위에서 말한 블랙박스와 입력, 출력이 의도된 것이라는 것을 알 수 있다.)

프로그래밍, OS, 여러 현실 세계의 요소에서도 이러한 추상화가 사용된다.

#### 인공지능

LLM은 수많은 입력을 통해서 어떤 단어가 주어졌을 때, 어떤 단어가 뒤에 오는지 최상의 추측을 만드려고 설계된다.

강의에서는 일반적인 AI 사용을 금지한다. 직접적인 답변을 제공해주지 않게 만들어진 CS50 Duck 만 사용할 수 있다. 

#### 프로그래밍

의사 코드를 사용해서 알고리즘을 작성할 수 있다.

실제로 기계에게 명령하기 위해서는 프로그래밍 언어를 사용해야 한다.

실습을 위해 스크래치를 사용하는데, 이는 불필요한 문맥을 줄이기 위해서이다. (C의 헤더, 괄호, 세미콜론 등의 표현은 집중을 방해한다.)

프로그래밍에는 필수적으로 명령문(변수, 기능 사용), 조건문, 반복문이 포함된다.

프로그래밍을 할때는 작은 단계에서부터 점진적으로 해결해야한다.

### 느낀점

 - 놀랍게도, 생각보다 재미있다.
   - 강의에서 다루는 흐름이 이해하기 쉽고, PPT 띄워놓고 읽는게 아니라 집중이 잘 됨.
 - 나는 번역해서 보았지만, 영어 어휘도 쉬운 편이라 영어 공부할 때 써도 좋을듯. 