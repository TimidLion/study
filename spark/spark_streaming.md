**<주의>** 
- **3~4년 전에 정리한 내용이라, 최신 문서 기준으로 업데이트 필요**
- 잘못된 내용이 있을 수 있음
 
---

# Spark Streaming

## 정의 및 개념

- 실시간 데이터 스트림을 다루는 Spark API 의 확장 기능 중 하나.
- 실시간 데이터 스트림(Kafka, Twitter, HDFS/S3 등)을 처리한 후 다양한 형식(HDFS, DB, Dashboard 등)으로 저장한다.
- map, reduce, join, 그리고 window등의 operation들을 적용한 복잡한 알고리즘들도 처리할 수 있다.
- Spark SQL은 물론이며, Spark ML이나 Spark GraphX같은 다양한 Spark API들도 적용 가능하다.

- DStream(discretized stream) : 스트림 데이터를 일정 단위로 쪼개어 미니 배치로 처리하는 구조다.
    - DStream을 소스(HDFS, Kafka 등)로부터 Receiver가 받아서 인풋으로 사용
    - DStream은 사실 RDD들의 연속(Sequence)임.
    - 따라서 기존 RDD에 쓰던 operation들을 사용할 수 있다.
    - 또한 DStream은 상태를 갱신하고 새로운 데이터를 계산하는 일종의 실시간 프로그램이라고 볼 수 있다.
- StreamingContext : DStream의 생성과 스트리밍 처리 시작, 종료 등을 수행
    - JVM 하나에 하나의 StreamingContext가 있을 수 있다.
- Receiver : 소스(HDFS, Kafka 등)로부터 input을 받아 DStream으로 Spark 의 메모리에 저장하는 오브젝트.
    - 하나의 Receiver 당 하나의 core를 쓰기 때문에 Receiver들용 core들과 연산용 core가 확보되어야 한다.
    - 데이터를 제대로 복제 및 저장 되었다고 신뢰할만한 소스에 신호를 보내는 경우. 주로 신호를 받을 수 있는 소스일 때 이뤄진다.
    - 신호를 보내지 않는 경우는 신호를 받을 수 없는 소스이거나, 사용자가 신호를 보낼 필요가 없다고 판단한 경우이다.
    
- Stateful Streaming : 시간에 따라 유입되는 미니 배치들로부터 상태(State)를 유지하게 하는 상황. 예를 들어 이를 도와주는 함수에는 updateStateByKey 같은 함수가 있다.
    1. 시간이 흐르면서 새로운 데이터들이 배치로 DStream에 유입된다.
    2. 새로운 배치 데이터가 유입될 때마다 함수에 따라 상태(State)를 변경한다.
- Checkpoint : 시스템 실패에도 데이터 복구가 용이해야하기 때문에 두가지 체크포인트를 가진다.
    1. 메타데이터 체크포인트
        1. Streaming app. 을 만든 conf.정보들, DStream 연산 정보, 실행되지 않은 배치 정보 들을 담고 있다.
        2. streaming app. 을 돌리는 드라이버의 노드가 실패했을 때를 대비해서 결함에 강한 HDFS같은 저장장치에 저장된다.
    2. 데이터 체크포인트
        1. 메타데이터와 같이 stateful transformation의 중간과정들을 믿을만한 저장소(HDFS)에 잠시 저장한다.
        2. stateful transformation의 복구과정에서 RDD가 과거의 배치들을 지나치게 거슬러 올라가면서 복구하지 않게 하기 위해서 필요하다.

## 특징

- 기존의 실시간 데이터 스트림을 다루는 API들과는 다르게 배치 단위로 쪼갠 인풋을 받는다.
- 배치 단위로 쪼개어져있기 때문에 기존의 RDD에 적용할 수 있는 연산들을 각 쪼갠 단위에 적용하여 쓸 수 있다.
- 이전의 RDD 데이터와 스트리밍 데이터를 결합해서 사용할 수 있다.
- 인풋 스트림이 batch job을 거쳐서 각 노드들에 나누어지기 때문에 동시성을 염려할 필요 없다.
    - 예를들어 기존의 스트리밍 처리 API는 A웹페이지와 B웹페이지의 클릭수 비율을 구할 때 A웹페이지가 누락되거나 지연되었을 때에는 의도와 다른 비율이 나온다.
    - 그러나 Spark Streaming에서는 batch 단위에 모든 A페이지와 B웹페이지 클릭수가 포함되어있기 때문에 걱정할 필요 없다.
- 데이터가 '도착'해야만 DStream을 받아오기 시작한다.
    - [t,t+1) 사이의 데이터가 업뎃되었다고 더 늦은 타이밍(t+3)에 오는 경우를 처리하는 두 가지 방법이 있다.
        1. 약간의 추가시간을 더해서 맞는 배치에 데이터들이 신호가 오도록 한다.
        2. 추후에 reduce할 때 더해준다.
        
    
- Spark Streaming에 있어서 Cluster mode와 Client mode 차이
    - Client mode로 돌리게 된다면 driver가 돌고 있는 머신이 꺼졌을 때 Spark Streaming application도 꺼지게 되어서 문제가 생긴다.
    - 반면, Cluster mode의 경우에는 driver가 돌고 있는 머신이 꺼져도 다른 node에서 자동으로 재시작하게 된다.
        - driver process를 재시작하게 되면, 체크포인트에 저장된 StreamingContext를 읽어들여 복구를 하게 된다.
        - 이 때, 이전상태가 있으면 저장된 상태를 불러오고 StreamingContext 초기화를 생략한다.
        - 이전 상태가 없으면 StreamingContext 초기화를 진행한다.
    - driver를 재시작했을 때 발생하는 데이터 손실을 막으려면, 리시버는 데이터를 처리하기 전에 write-ahead-log에 저장할 수 있다.
        - 리시버는 write-ahead-log에 데이터를 저장하고 나서야 데이터를 받았다는 확인 신호를 보낸다.
        - 따라서 복구할 때에도 리시버가 아직 못 가져온 데이터를 이 write-ahead-log에서 가져오므로 데이터 유실이 발생하지 않는다.
            - kafka direct connector의 경우에는 카프카가 해당 기능을 제공하기 때문에 따로 해줄 필요가 없다.
            - 기본 설정으로는 비활성화 되어 있다.
            
        

## 구조

![spark_streaming_structure](https://user-images.githubusercontent.com/10977644/166201037-e2f5bdab-9101-41f7-b81b-13b3add596a8.png)

1. 실시간 스트림 데이터 소스(Kafka, Flume, sockets 등)로부터 실시간 스트림 데이터를 Spark Streaming안의 object인 Receiver가 입력받는다.
2. Spark Streaming API에서 실시간 스트림 데이터를 일정 시간 간격마다 mini-batch로 DStream을 만든다.
3. DStream에 operation 및 Spark API들을 적용할 수 있다.
4. 적용한 결과를 HDFS, DB, Dashboard 등으로 내보낸다.

![spark_streaming_architecture](https://user-images.githubusercontent.com/10977644/166201070-9d849c4f-813c-417d-89d7-d857bfd66462.png)

- master : DStream 계보를 추적, 새 RDD partition을 위한 task를 스케줄 한다.
- worker nodes : 데이터를 받고 계산된 RDD들을 저장한다. task를 실행한다.
- client library : 데이터를 시스템에 보내기 위해서 쓰인다.

- 적용할 수 있는 두 종류의 Operation들
    - transformation operation : 하나 또는 그 이상의 DStream으로부터 DStream을 만드는 operation이다.
        - stateless : state를 고려하지 않는 형태의 operation이다. map, reduce, groupBy, join 등등이 있다.
        - stateful : state를 고려하는 형태의 operation이다. 예를들어 windowing이 있다. windowing은 일정 배치묶음에 간격을 두고 operation을 적용하는 것이다.
    - Output operation : 외부 시스템에 데이터를 저장하는 Operation. write같은 기능들이다.
