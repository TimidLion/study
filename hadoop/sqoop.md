# Sqoop

## 정의
- RDBMS 내 파일 -> HDFS 로 옮겨주는 Tool (이름은 SQL to Hadoop 의 약자) [1][3]
- (현재는 _archived_ 되었다) 

## 구조
### Sqoop Import 방식 [2]
1. (어떤 서버 GW에서) Sqoop Client 가 Import 요청을 보낸다.
2. RDBMS의 메타 데이터를 가져와서, ORM 형태로 생성
  - ORM클래스에는 대상 테이블의 컬럼을 자바 변수로 맵핑 & 맵리듀스 잡 실행에 필요한 직렬화 메소드 생성
3. (2) 기반으로 MapReduce 잡 실행 요청. 
4. JDBC 접속 후, SELECT 쿼리를 실행
  - 쿼리 실행시, primary key 최소, 최대값 기반으로 균등하게 분배. (맵퍼의 수나 primary key는 당연히 사용자 지정 가능)
5. 각각의 Task는 쿼리문 실행 결과를 HDFS 로 저장.
6. Hive 테이블 생성
7. Hive 테이블 데이터 경로로 로딩

## 문법
#### Split-by 와 Mapper
- mapper 옵션을 통해 _병렬성_ 옵션을 줄 수 있다. [5]
- `split-by` 옵션과 직접 parallell sqoop 기준 설정도 가능하다. 단, 한 개만 지정 가능하다 (default는 index 키) [4][7]
    - split-by 키가 없으면 꼭 mapper 1로 설정해줄 것 [6]

## 출처
- [1] https://github.com/apache/sqoop
- [2] https://excelsior-cjh.tistory.com/56
- [3] https://guide.ncloud-docs.com/docs/ko/hadoop-chadoop-4-1
- [4] https://gritmind.blog/2020/09/29/sqoop_start/
- [5] https://sqoop.apache.org/docs/1.4.6/SqoopUserGuide.html#_syntax
- [6] https://stackoverflow.com/questions/33994476/can-we-split-sqoop-job-by-multiple-column-combination
- [7] https://community.cloudera.com/t5/Support-Questions/Sqoop-import-composite-primary-key-and-textual-primary-key/td-p/145994
