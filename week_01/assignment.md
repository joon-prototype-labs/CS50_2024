### References
- https://learning.edx.org/course/course-v1:HarvardX+CS50+X/block-v1:HarvardX+CS50+X+type@sequential+block@71a958764ce040ddb6ea30553bdc7ed1/block-v1:HarvardX+CS50+X+type@vertical+block@af6e917ee267462e8bf2f026e5d2d9e0
- https://cs50.harvard.edu/x/2024/psets/1/
- https://manual.cs50.io/
- https://cs50.readthedocs.io/style/c/

### Note

- 그래도 프로그래밍 경험이 있으니까 선택가능한 난이도중에서 어려운 걸 골랐다.
- `hello.c`, `mario.c` 는 별로 어렵지 않았다.
- `credit.c`는 조금 걸렸다.
  - 시간이 걸린 이유
    - 요구사항이 좀 있음.
    - 직관적으로 떠올렸던, string의 앞 글자를 읽어서 판단하는 방법을 사용할 수 없었다.
        - 아직 배열을 배우지 않았으므로 해당 방식은 불가능하다.
        - 그래서 작업 단위로 나눠서 실행을 못하고, 순차적으로 값을 읽으면서 처리해야 했는데, 평소에 선호하던 방식이 아니라 좀 걸렸다.
          - 뭐 성능은 이런 방식이 더 좋아보이지만
    - 요구사항 분석
      - 입력이 유효한가?
        - 숫자만 입력 가능하며, 모든회사는 0으로 시작하지 않으므로 long으로 입력을 받을 수 있다.
      - 번호가 유효한가?
        - 체크섬 실행. 원하는 자릿수의 값을 구하는 기능이 필요하다.
      - 어떤 회사인가?
        - 숫자 자릿수와 시작 번호로 식별 가능하다.
    - 구글링도 좀 함.
      - 내가 수학을 못하니까, 가끔 수학 지식을 요구하는 곳에서 막히는 건 어쩔 수 없음. 공부를 하던가 해야지. 

- 코드 스타일이 좀 맘에 안들긴 함.
  - 특히 대활호 부분, 보다보니 눈에 익긴 하지만, 라인 수가 너무 길어지는 것 같아서 별로임.

### C Code

-삭제됨-