#### 7.1 for 내장의 기본 요소
Q1. 6.9.1 의 꼬리재귀와 무한 컬렉션에 나오는 Stream이란?

A1. lazy 컬렉션으로, 머리(head)만 평가(계산)해두고, 필요시에만 꼬리(tail) 부분을 계산하게된다. (메모리 아낄 수 있음)

- 다만, Stream도 결국엔 평가(계산)한 값은 전부 저장하기 때문에 이 경우에도 많은 메모리를 사용
- for 내장을 사용시, 메모리에 한 번에 한 줄만 유지하기 때문에 더 이득!

#### 7.2 for 내장: 내부 동작
- 사실 for 내장은 foreach, map, flatMap, withFilter를 호출하는 것이랑 같다
Q2. 그렇다면 왜 for 내장을 제공하는가?
  A2. 여러 API를 호출하는 것보다 for 내장이 읽고 쓰기 편해서 ㅎㅎ

- **제너레이터** == for 내장으로 무언가를 **반환**하는 경우 (yeild 포함)
- **for 루프** == for 내장을 그냥 돌고 **반환**하지 않는 경우

<for 내장 구문은 사실...>
- **(yeild가 뒤에 없는 단일 for 내장) == (foreach)**
- **(yeild가 뒤에 있는 단일 for 내장) == (map)**
- **(2이상의 제너레이터(yeild)가 있는 경우) == (마지막을 제외한 모든 제너레이터는 flatMap, 마지막은 map)**
- **(제너레이터(yeild)에 if 구문같은 조건이 들어가는 경우) == (사실상 withFilter가 추가된 것)**

#### 7.3 for 내장의 변환 규칙
- 7.2 에서 설명한 내용을 풀어서 예제로 확인
- **전체 변환 규칙**
Q3. 대체 왜 이걸 알아야 하는가? (편하게 쓰려고 하는 거라면서...)
A3. 자주 할 필요는 없지만, 디버깅할 때 유용하다. (아마 내부 돌아가는 방식을 알기 + 7.4 와 같이 for 내장이 적용 가능한 타입 판단이라던가...?)

- [for comprehension에 대한 scala doc](https://docs.scala-lang.org/tour/for-comprehensions.html#inner-main)
- https://www.baeldung.com/scala/for-comprehension
```md
Like many other Scala constructs, the for-comprehension comes directly from Haskell. 
Its use goes far beyond simply looping over collections, 
helping us to deal with the complexity of the syntax when using a functional approach to programming.
```

#### 7.4 Option과 다른 컨테이너 타입
- 7.3 까지는 컬렉션 타입만 썼지만, 사실 **foreach, map, flatMap, withFilter**가 구현되어 있는 타입이면 for 내장 사용 가능!
- 따라서... Option도 for 내장 사용 가능!
    - (돌아보기 : Option을 사용한 메서드는 Option 비어 있지 않은 경우에만 적용되기 때문에 _우아하다._)
- 비슷한 타입으로는 Either, Try, Validation 등이 있다
- Option : (있거나 or 없거나:None)
- Either : (A or B)
    - 왜 예외를 던지는 게 아니라 Either를 쓰는가...? -> **참조 투명성**을 해치기 때문
    ```scala
    def addInts(s1: String, s2: String): Int =
    | s1.toInt + s2.toInt

    | for {
    |    i <- 1 to 3
    |    j <- 1 to i
    | } println(s"$i+$j = ${addInts(i.toString, j.toString}")

    // 아래가 문제되는 케이스
    addInts("0", "x")
    ```
    - 조금 억지스러운 예제(라고 저자가 말함)이지만 함수 정의만 보고 이런 문제가 있다는 걸 알기 쉽지 않음
    - 따라서 Either를 사용해서 아래와 같이 구현해서 '실패'에 대해서도 정의
    ```scala
    def addInts(s1:String, s2:String): Either[NumberFormatException, Int] =
    | try {
    |    Right(s1.toInt + s2.toInt)
    | } catch {
    |    case nfe: NumberFormatException => Left(nfe)
    | }
    ```
- Try : ((숨겨진 Throwable) or Success인 경우에만 T 적용)
    - 위의 Option, Either 가 두 개의 인자를 받았는데, Try 는 하나만 받는다. (하나는 Throwable로 고정)
    - 실패하면 Failure(AssertError타입) 을 반환, 성공하면 Success(T) 를 반환
- Validation : (여러 오류를 수집해서 한 번에 처리해야하는 경우)
    - 외부 라이브러리를 사용해야한다!
 
#### 7.5 마치며
- for 내장이 의미하는 바 이해
- for 내장이 **컬렉션**뿐만 아니라 다양한 **컨테이너 타입(Option, Either, Try, Validation 등)**에도 적용 가능
- 따라서 발생할 수 있는 예외를 **실체화**해서 표현 (미리 볼 수 있음)
- 14, 15 장 그리고 17장에서 유용하게 쓰임
