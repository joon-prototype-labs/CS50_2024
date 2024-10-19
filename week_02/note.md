### References

- 추가 필요
- https://cs50.harvard.edu/x/2024/notes/2/
- https://manual.cs50.io/
- https://cs50.readthedocs.io/style/c/

### Note

#### 컴파일 과정

- c에서는 make를 사용하는데, 빌드 자동화 소프트웨어로, 컴파일러는 아니다.
  - 컴파일러를 실행하고, 복잡한 의존성 관리, 링킹, 실행 파일 이름 지정하는 등의 복잡한 것들을 자동화 해준다.
  - 컴파일러는 GCC, Clang 등이 있다. 강의(VS Code 환경을 사용하므로)에서는 더 최신 식이고, 컴파일러 설명이 친절한 Clang을 사용한다.
- C에서는 컴파일 시 다음과 같은 동작이 수행된다.
  - preprocessing
    - 전처리 지시문 (특히 `#include`)를 처리한다. (매크로 치환도 여기서 함.) 
    - 코드 상단에 작성된 헤더 파일(`.h`)을 코드 본문에 붙여넣기 한다.
      - 헤더 파일에는 함수 프로토타입이 정의되어 있고, 라이브러리로 제공되는 함수의 프로토타입을 편리하게 재사용하기 위해 사용한다.
  - compiling
    - 프로그램이 어셈블리 코드로 변환된다.
  - assembling
    - 어셈블링은 컴파일러가 어셈블리 코드를 기계 코드로 변환한다.
  - linking
    - 포함된 라이브러리의 코드도 기계 코드로 번역되어 코드와 결합된다. 실행 가능한 프로그램이 된다.
      - (실제로는 이미 컴파일된 라이브러리 코드를 결합한다.)
      - (동적 링크나 정적 링크는 강의에서 다루지 않는데, 컴퓨터 밑바닥의 비밀 책에서 잘 설명해줌.)

#### 디버깅

- 코딩을 하면서 여러 문제를 분석하기 위해서 버그를 찾는 과정인 디버깅을 할 수 있다.
- 간단하게는 `printf`와 같은 출력 기능을 사용할 수 있으나, 복잡한 기능에서는 추천하지 않는다.
- 이를 위해 디버거를 사용할 수 있다. step over, step into, step out 등의 기능 사용 가능.

#### 배열

- 컴퓨터의 사용 가능한 메모리 양은 제한되어있다.
  - 메모리는 단순하게 거대한 1차원 배열로 생각할 수 있다.
- 최소 1byte 단위로 저장되며, 배열을 사용해서 동일한 타입을 연속해서 저장할 수 있다.
  - (그래서 bool도 1byte 저장공간을 가진다.) 
  - (저장은 1byte 단위더라도 쓰기, 읽기는 다를 수 있음.)
- 배열은 특정 타입을 일정 개수로 연속된 공간에 저장된다.
  - (프로그래밍 언어에 따라 배열의 여러 타입이 배경이 가능할 수 있으나, 이는 추상화를 통해 제공되는 것이다.)

#### string
- string의 경우 특수한 규칙을 가지는 char 배열이다.
- 배열의 끝이 `NUL`(전부 0인 1byte 값)인 char 배열이다.
- 이를 통해 문자열의 끝을 확인할 수 있다.
  - string은 배열의 크기가 정해져있지 않으므로 이런 값이 없으면 끝을 알 수 없다.
- 그래서 string의 크기는 (글자수 + 1) byte이다.
- C 표준 라이브러리에서 `string.h`, `ctype.h` 와 같이 문자열을 다루는 편리한 기능을 제공해준다.

#### CLI arguments

- CLI에서 프로그렘에 전달되는 인수
- `int main(int argc, string argv[])`로 선언해서 가져올 수 있다.
  - argv 0번 인덱스에서는 명령어 이름이 들어가므로, 추가 인수가 사용되는 경우 argc의 값은 2 이상이다.

#### Exit Status

- 프로그램은 종료 시 특별한 종료 코드를 - int 값을 가지는 - 반환한다. 
- 0의 경우 정상적으로 수행됨. 그 외의 경우 비정상적인 수행을 의미한다.
  - 0 외의 경우, 에러 코드로서 사용되기도 한다.

#### Cryptography

암호학은 생략함.

### 리뷰

- 뭔가 깊이있게 다루지는 않는다. 
- 유명한 대학 강의라 좀 기대하긴 했데, 아무래도 교양이나 기초 강의라 그런듯.
- 영어공부 해야하는데...