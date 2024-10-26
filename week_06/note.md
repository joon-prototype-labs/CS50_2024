### References

- https://cs50.harvard.edu/x/2024/notes/6/

### Note

- 강의에서 다룬 파이썬 문법은 거의 다 아므로 생략
- Loop문 내부에서 정의한 변수나 if문을 Loop 바깥에서 사용하는건 아무리봐도 적응이 안된다.  

### 리뷰

- C 다루다가 Python 보니까 확실히 단순하고 기능이 많다. 
- 강의를 보다 문득 든 생각인데, C나 Python과 달리 Java나 Kotlin은 메서드나 함수의 순서를 중요하게 생각하지 않는데, 이는 언어의 실행 특징이 다르기 때문이라는 걸 느낌. 
  - Java나 Kotlin은 Bytecode로 번역되어서 이미 Symbol 정보가 상단에 맞춰서 컴파일된다. 그렇기 때문에 때문에 Python과 달리 순서가 중요하지 않음. (실행 시점에는 순서대로 읽으면 Symbol을 먼저 읽기 때문)
    - 반대로 Python은 중간 코드가 있는지는 모르겠지만, bytecode처럼 Symbol을 Table에 따로 저장하지는 않기 때문에 함수 간의 순서를 신경써야하는걸로 보임.
    - C도 마찬가지로 위에서 아래로 왼쪽에서 오른쪽으로 읽기 때문에 이런 특징이 있다. (그렇다고 Python이랑 C랑 실행 과정이 같은건 아니고, 비슷한 점이 있다는 뜻임.)

- 여담으로 재밌는걸 찾았는데, https://cs50.harvard.edu/x/2024/notes/not-found/ 처럼 없는 경로에 들어가면 https://http.cat/404 이미지를 보여준다.