# RDD (Resilient Distributed Dataset)

- Spark 의 기본 데이터 구조
- `a collection of elements partitioned across the nodes of the cluster that can be operated on in parallel` [1]
- 클러스터의 노드에 나눠서 저장된 병렬처리로 처리되는 요소들의 모음

## 특징
1. **데이터 추상화**
    - 데이터는 클러스터에 흩어져 있지만, 하나의 파일인 것처럼 사용이 가능
2. **탄력성 & 불변성 (Resilent & Immutable)**
    - 여러 노드 중 하나가 망가져도 데이터가 Immutable 하면 복원 가능
    - Immutable : RDD1 -> RDD2 일 때, RDD1이 사라지는 것이 아닌 RDD2 가 새로 생김. (이 과정은 DAG:비순환 그래프로 남는다.)
3. **Type-safe**
    - 컴파일시 타입 판별이 가능
4. **Unstructured / Structured Data**
    - Unstructured Data : 텍스트(자연어, Raw 로그) 등
    - Structured Data : RDB, DataFrame
5. **Lazy**
    - Action 을 하기 전까지 Transformation 은 실행되지 않음. [2]
    - Action : 지연실행 됨. 결과를 수집해서 반환(데이터셋에 연산을 적용해서 드라이버 프로그램으로 결과를 돌려준다.)
    - Transformation : 현존하는 데이터셋으로 새 데이터셋을 만들기. 새로운 RDD가 생성된다. (DAG를 만듦)
      - Narrow Transforamtion : 1:1로 변환됨. 다른 열/파티션을 참조할 필요 X. (ex. `map()`, `flatmap()`, `filter()` 등)
      - Wide Transformation : 셔플링(Shuffling)이 일어남. (ex. `union()`, `groupBy()` 등)

## 관련 함수
#### Key Value RDD
- `reduceByKey()` 와 `groupByKey()`의 차이 [3]
    - `groupByKey()` : 각 키의 모든 값을 메모리로 가져옴. 순서 보장 X (aggregateByKey 나 foldByKey 추천)
    - `reduceByKey()` : 각 맵퍼에서 로컬로 병합을 수행
- key-val 형태기 때문에, join도 사용 가능
- key 를 바꾸지 않는 경우 `map()`대신 `mapValues()` 사용 추천 (파티션 유지 가능)

#### `collect()`
- RDD 안에 있는 값을 전부 가져온다.
- 당연히 프로덕션 환경에서는 쓰지 말아야한다.

## 출처
- [1] https://spark.apache.org/docs/latest/rdd-programming-guide.html
- [2] https://spark.apache.org/docs/latest/rdd-programming-guide.html#rdd-operations
- [3] https://thebook.io/006908/part01/ch04/03/03/01/
