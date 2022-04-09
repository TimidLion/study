# RDD (Resilient Distributed Dataset)

- Spark 의 기본 데이터 구조
- `a collection of elements partitioned across the nodes of the cluster that can be operated on in parallel` [1]
- 클러스터의 노드에 나눠서 저장된 병렬처리로 처리되는 요소들의 모음

## 특징
1. 데이터 추상화
  - 데이터는 클러스터에 흩어져 있지만, 하나의 파일인 것처럼 사용이 가능
2. 탄력성 & 불변성 (Resilent & Immutable)
  - 여러 노드 중 하나가 망가져도 데이터가 Immutable 하면 복원 가능
  - Immutable : RDD1 -> RDD2 일 때, RDD1이 사라지는 것이 아닌 RDD2 가 새로 생김. (이 과정은 DAG:비순환 그래프로 남는다.)
3. Type-safe
  - 컴파일시 타입 판별이 가능
4. Unstructured / Structured Data
  - Unstructured Data : 텍스트(자연어, Raw 로그) 등
  - Structured Data : RDB, DataFrame
5. Lazy
  - Action 을 하기 전까지 Transformation 은 실행되지 않음. [2]
  - Action : 데이터셋에 연산을 적용해서 드라이버 프로그램으로 결과를 돌려주기
    - `return a value to the driver program after running a computation on the dataset`
  - Transformation : 현존하는 데이터셋으로 새 데이터셋을 만들기
    - `create a new dataset from an existing one`

## 출처
[1] https://spark.apache.org/docs/latest/rdd-programming-guide.html
[2] https://spark.apache.org/docs/latest/rdd-programming-guide.html#rdd-operations
