# 데이터 메모리 캐싱
- 여러번 사용하는 데이터의 경우, 데이터를 메모리에 캐싱해두고 사용 가능
- 어떤 Storage Level 을 골라야 하는가? [1]
  - MEMORY_ONLY : 기본값. CPU 효율 좋은 편.
  - MEMORY_ONLY_SER : 기존 메모리 저장 방식 + 다른 적절한 직렬화 방식 라이브러리 적용 (ex. Java and Scala)
  - 진짜 가급적 DISK 에는 저장 X
  - `replicated storage levels` 는 빠른 데이터 복구를 원하면 사용하는 것 추천.
  
## `Cache()`
- default storage 레벨 사용
- RDD : MEMORY_ONLY
- DF(DataFrame) : MEMORY_AND_DISK

## `Persist()`
- 스토리지 레벨을 사용자가 원하는대로 지정

## 출처
- [1] https://spark.apache.org/docs/latest/rdd-programming-guide.html#which-storage-level-to-choose
