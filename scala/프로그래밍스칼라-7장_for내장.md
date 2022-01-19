#### 7.1 for 내장의 기본 요소
Q1. 6.9.1 의 꼬리재귀와 무한 컬렉션에 나오는 Stream이란?

A1. lazy 컬렉션으로, 머리(head)만 평가(계산)해두고, 필요시에만 꼬리(tail) 부분을 계산하게된다. (메모리 아낄 수 있음)

- 다만, Stream도 결국엔 평가(계산)한 값은 전부 저장하기 때문에 이 경우에도 많은 메모리를 사용
- for 내장을 사용시, 메모리에 한 번에 한 줄만 유지하기 때문에 더 이득!

#### 7.2 for 내장: 내부 동작
- 사실 for 내장은 foreach, map, flatMap, withFilter를 호출하는 것이랑 같다
Q2. 그렇다면 왜 for 내장을 제공하는가?
  A2. 여러 API를 호출하는 것보다 for 내장이 읽고 쓰기 편해서 ㅎㅎ

- **제너레이터** == for 내장 뒤에 yeild가 붙은 경우
- **for 루프** == for 내장 뒤에 yeild가 없는 경우

<for 내장 구문은 사실...>
- **(yeild가 뒤에 없는 단일 for 내장) == (foreach)**
- **(yeild가 뒤에 있는 단일 for 내장) == (map)**
- **(2이상의 제너레이터(yeild)가 있는 경우) == (마지막을 제외한 모든 제너레이터는 flatMap, 마지막은 map)**
- **(제너레이터(yeild)에 if 구문같은 조건이 들어가는 경우) == (사실상 withFilter가 추가된 것)**

#### 7.3 for 내장의 변환 규칙
- 7.2 에서 설명한 내용을 풀어서 예제로 확인
- **전체 변환 규칙**
Q3. 대체 왜 이걸 알아야 하는가? (편하게 쓰려고 하는 거라면서...)
A3. 자주 할 필요는 없지만, 디버깅할 때 유용하다.

- [for comprehension에 대한 scala doc](https://docs.scala-lang.org/tour/for-comprehensions.html#inner-main)

#### 7.4 Option과 다른 컨테이너 타입
...(작성중)
