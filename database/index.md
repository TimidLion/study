## DB Index

- **DB Index 란?** : DB의 테이블 값을 시간/공간적으로 비용이 적게 들게 가져올 수 있도록 만든 데이터 구조. [3]
- **왜 써야 하는가?** : 데이터가 많을 때, 빠르게 값을 가져오고, 공간적으로도 절약할 수 있게 하기 위해 [2]


## Index 키 검색 방법
- B-Tree인덱스를 사용하는 경우, = 일치 검색 또는 앞부분 일치를 이용한 검색의 경우 인덱스를 사용할 수 있고 부등호 < , > 비교도 인덱스를 사용할 수 있으나 뒷부분 일치를 이용한 검색은 불가능하다. [4]

## DB 별 index 설정
### MySQL
- Indexing 에 B+ Tree 형태를 사용
- Clustered Index (자동 설정) / Non Clustered Index (개뱔자가 설정) [1]
- Index 종류 [4]
  - Primary Key Index : PK(컬럼에 NULL과 중복을 허용하지 않음)를 기준으로 만들어지는 인덱스
  - Secondary Key Index : PK를 제외한 모든 인덱스

## 출처
- [1] https://kyungyeon.dev/posts/66
- [2] https://brunch.co.kr/@skeks463/25
- [3] https://en.wikipedia.org/wiki/Database_index
- [4] https://jeong-pro.tistory.com/242
