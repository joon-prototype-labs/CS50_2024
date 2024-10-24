### References

- https://cs50.harvard.edu/x/2024/notes/4/
- https://manual.cs50.io/
- https://cs50.readthedocs.io/style/c/

### Note

`#include<cs50.h>`라는 보조바퀴를 뗴어낼 시간...

#### 16진법 - Hexadecimal

- 한 자리수가 16개의 수를 표현할 수 있다.
- 16진수의 한 자리수가 2진수의 4자릿수와 일치하기 때문에 수를 2진법 수를 짦게 표현하기 편하다.
  - 다른 2^N의 자리수도 쓰이긴 하지만, 2자리수의 16진수로 1Byte 표현이 가능하기 떄문에 16진수를 많이 쓴다.
- 사용하는 10진수와 비교하기 위해서 `0x{16진수 수}`로 표현한다.

#### 포인터

포인터는 어떤 값의 주소를 담고 있는 변수이다.

##### string과 char *

`#include<cs50.h>`에서 제공하는 string은 사실 `typedef char *string`을 사용해서 이름을 바꾼 `char *`이다.

char *의 경우, C에서 조금 특별하게 다룬다. `%s`나 그런거에서...

##### copy와 swap(call stack)

- 포인터 변수를 다른 포인터 변수에 할당하는 경우(`int *x = y`),    
  - 참조하는 주소 값이 복사하여 저장하기 떄문에 참조하는 값이 변경된 경우, 둘 다 영향을 준다.
  - 둘 다 같은 원본을 참조하므로, 얕은 복사가 발생한다. 

- 원본이 2개가 되도록 복사하고 싶다면 `malloc`을 사용해서 새로운 원본을 만들어서 참조해야 한다.

- call stack과 swap
  - 함수 호출 시 stack에 frame이 쌓이면서 연산을 수행하는데, 이 경우 파라미터 값을 호출자에서 전달해준다.
    - 그냥 int와 같이 heap에 선언되지 않은 변수라면 pass by value(값 전달, 깊은 복사)가 발생하므로 원본에 영향을 주지 않는다.
  - 따라서, swap과 같이 원본에 영향을 줘야하는 함수는 역참조 연산자(`*{변수}`)를 사용해서 변수의 원본을 바라보는 포인터 값을 받아야 한다.
    - 또한 원본이 너무나 많은 메모리를 차지해서 복사해서 넘겨줄 때 낭비가 심해서 사용된다.

##### C에서의 표현

- `{타입} *{변수}`(Pointer Variable Declaration): 특정 변수를 바라보는 포인터 변수를 선언.
  - 관례 상으로 변수 옆에 *를 붙인다.
- `&{변수}`(Address-of Operator): 변수의 주소값을 가져온다.
- `*{변수}`(Dereference Operator): 해당 (포인터) 변수의 값으로 가지는 주소의 값을 가져온다. 
- &는 앰퍼샌드, *는 스타라고 발음함.

```c
int n = 50;

int *p = &n; //&n을 사용해 n의 주소값을 반환.

char *s = "HI!"; // 문자열의 경우 컴파일러가 알아서 첫번째 주소값을 할당해준다. (여기선 s)

// s[N]와 같은 배열은 *(s + N)과 동일하다. 컴파일러가 변환해준다.
printf("%c\n", *s);
printf("%c\n", *(s + 1));
printf("%c\n", *(s + 2));
```

#### `scanf`, malloc, Valgrind

`#include<cs50.h>`에서 제공하는 `get_{타입}` 함수는 사실 `scanf`와 여러 기능을 추가하여 추상화해준 것이다.

다음과 같이 `get_string`과 비슷한 기능을 구현할 수 있다.
```c
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    char *s = malloc(4);
    if (s == NULL)
    {
        return 1;
    }
    printf("s: ");
    scanf("%s", s);
    printf("s: %s\n", s);
    free(s);
    return 0;
}
```

`malloc`은 Heap에 새로운 메모리 영역을 할당하는 기능으로, 런타입에 값을 받아서 처리해야하기 때문에 필요하다. (`char *`은 크기가 고정되지 않아 할당이 필요한 것으로, `char[]` `int` 타입과 같이 범위가 고정된 경우 필요하지 않다.).    
`malloc`로 새로운 영역을 할당한 이후, `free`를 사용해서 영역을 해제해야 한다. 그렇지 않으면 메모리 누수가 생긴다.    
(`#include <stdlib.h>` 선언을 해야 사용 가능하다.)

Valgrind는 프로그램에서 메모리 관련 문제가 있는지 확인할 수 있는 도구이다.

```c
#include <stdio.h>

int main(void)
{
    char s[4];
    printf("s: ");
    scanf("%s", s);
    printf("s: %s\n", s);
}
```

#### 파일 I/O

이제 메모리를 다룰 수 있으므로 파일 또한 다룰 수 있다.

아래 코드는 파일을 복사하는 (`cp` 커멘드와 비슷한 역할) 프로그램의 코드이다.

```c
#include <stdio.h>
#include <stdint.h>

typedef uint8_t BYTE;

int main(int argc, char *argv[])
{
    FILE *src = fopen(argv[1], "rb");
    FILE *dst = fopen(argv[2], "wb");

    BYTE b;

    while (fread(&b, sizeof(b), 1, src) !=0)
    {
        fwrite(&b, sizeof(b), 1, dst);
    }

    fclose(dst);
    fclose(src);
}
```

### 리뷰

- 커리큘럼을 진행하기 위해서 구체적인 부분을 추상화(`cs50.h`)하고, 나중에 필요할 때 설명하는 진행 방식이, 커리큘럼에 신경쓴게 보여서 좋았음.
  - 구체적인 부분을 사용하고 무시하라고 할 수도 있는데, 추상화 시켜서 신경쓸 필요조차 없게 한 거
- 실습이 재미있음. 고민 많이 한 것 같음.
  - 여러가지 주제를 다루면서도 어렵지 않게 요구사항을 친절히 설명하고, 어느정도 구현 된 걸 제공해서 난이도를 낮춤.
  - 그러면서 강의 주제랑 잘 맞음.  