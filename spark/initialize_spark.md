## 용어
#### SparkConf
- 스파크 설정 옵션들에 대한 키와 값을 갖고있는 객체
- 사용자가 값들을 정의해서도 쓸 수 있다.

#### SparkContext
- Spark 클러스터와 연결시켜주는 객체
- 보통 SparkConf를 넘겨줘서 초기화한 SparkContext 를 `sc` 라고 초기화해주는 컨벤션이 있다.
- 프로그램 당 하나씩만 초기화 가능
  - `Only one SparkContext may be active per JVM` [1]

#### appName
- 클러스터 UI에 보이는 이름
  - `name for your application to show on the cluster UI` [1]

#### 스파크 아키텍쳐 [2]
<img width="500" alt="스크린샷 2022-04-11 오후 2 21 56" src="https://user-images.githubusercontent.com/10977644/162670054-50848218-0460-438d-b4bc-874bcb8da185.png">
- 클러스터 매니저 : 컴퓨터의 클러스터 리소스를 관리
- 스파크 클러스터 매니저에서 Spark Master 프로세스가 실행된다.
- Standalone 인 경우
  - **Master** 에서 스파크 마스터 프로세스 실행, Slave Node의 **Worker의** Executor에서 실제 Task 실행이 일어난다.
  - 일반적으로 클러스터에서 Spark application 을 실행할 때, `spark-submit` 을 이용해 실행하곤한다. 클러스터 매니저가 이 때 리소스 분배 및 실행 등을 관리. [4]

## 출처
[1] https://spark.apache.org/docs/latest/rdd-programming-guide.html#initializing-spark
[2] https://www.samsungsds.com/kr/insights/Spark-Cluster-job-server.html
[3] https://spark.apache.org/docs/2.1.0/submitting-applications.html#master-urls
[4] https://spark.apache.org/docs/latest/submitting-applications.html
