## Structured Streaming

![structured-streaming-stream-as-a-table](https://user-images.githubusercontent.com/10977644/166201342-933f4dbf-b017-4518-b930-9645cfe661a5.png)

![structured-streaming-example-model](https://user-images.githubusercontent.com/10977644/166201366-7d3cfbf2-3fcf-4ba1-8311-daf25bd70bcc.png)


- Structured Streaming : Spark SQL 엔진 위에 만들어진 실시간 데이터 스트림 처리 엔진.
    - Data stream에 새로 들어온 데이터가 이어지고, 이 데이터가 바로 인풋테이블의 새로운 행이 된다.
    - checkpointing과 WAL을 통해서 멈췄던(실패했던) 부분부터 재시작할 수 있다.
        - 디렉토리가 주어진 경우, stream을 받아오기 전에 멈췄던 부분부터 이어서 받아오게 된다.
        - 재시작했을 때 변경해도 되는 것과 안 되는 것들이 있다. [[3]](http://spark.apache.org/docs/latest/structured-streaming-programming-guide.html#recovery-semantics-after-changes-in-a-streaming-query)
    - multiStreaming을 하려할 때, readStream 여러개를 두고 각 readStream에 옵션을 줘서 할 수 있다.
- 장점
    - StreamingContext를 따로 설정해줄 필요 없다.
    - Structured Streaming은 스트림 데이터를 DataFrame과 Dataset으로 사용할 수 있다.
        - stream에서 받아온 DataFrame과 Dataset도 일반적인 Spark SQL의 DataFrame & Dataset을 쓰는 것 처럼 사용 가능하다.
    - EventTime에 대한 개념이 존재한다.
        - EventTime : 그 이벤트가 발생한 시간. 이벤트가 Spark에 도착한 시간은 Processing Time이다.
        - Spark Streaming의 경우에는 EventTime이라는 개념이 존재하지 않아서, 앞서 발생한 이벤트가 나중에 도착해도 도착한 시간대에 이벤트를 처리한다. (데이터 손실)
        - Structured Streaming의 경우에는 데이터에 timestamp가 포함되었을 경우에는 EventTime이라는 개념을 쓸 수 있다.
            - window-based agg인 경우 늦게 도착한 이벤트타임을 이전 타임에 업데이트 하는 방식으로 알아서 처리해준다.
            - watermark 기능도 있다. threshold보다 늦게 도착한 이벤트 타임에 대해서는 받지 않는 방식. [[4]](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html#handling-late-data-and-watermarking)
                - 정확히는 (배치 내의 가장 최근 시각 - threshold) > T 여야만 한다.
        - end-to-end exactly once semantic (Spark Streaming에도 있음)
            - end-to-end한 특성 : 드라이버가 실패하거나 할 때, 같은 지점에서 다시 시작할 수 있어야한다.
            - exactly once 한 특성 : 다시 작업을 해도 똑같은 결과가 나와야한다. (idempotent)
            - streaming sink들은 재작업을 위해 idempotent하게 디자인 되어있다.
            - Structured Streaming 엔진에서 모든 소스는 offset을 기억하고 저장해둔다 가정한다.
            - 엔진은 checkpointing과 WAL을 통해서 offset을 기록해둔다.
    
    ### **단점**
    
    - output의 방식(output sink)이 정해져있다.
        - Structured Streaming의 경우에는 지정된 방식의 output 과 output별 모드가 있다.
            - output mode는 ( Append : 새로운 행만 출력. 대신 기존의 result table은 변하지 않아야함 / Complete : 항상 다 처리된 결과/ Update : 업데이트된 행만 그대로 출력)가 있다.
            - 또한 output sink뿐만 아니라 적용된 query에 따라(e.x. agg, mapGroupWithState ) 다른 output mode를 지원한다. 링크[5]
        - 그러나 foreachBatch 등을 사용하여 output table을 배치 단위로 저장하게 해주어서 이 단점을 해결할 수 있다. (실험적 단계)
            - 이 경우 배치 단위의 rows들을 Dataset으로 하여 주어진 함수를 적용한다. 그리고 batch별로 id를 준다.
            - 즉, 아직 지원되지 않는 output이라해도 직접 배치단위의 Dataset을 저장하는 writer를 써서 코드를 짜면 된다.
            - Structured Streaming에서 제한된 일부 기능을 batch단위로 적용해 사용할 수도 있다.
        - 일부 지정된 output 방식은 디버깅만을 위한 모드라던가의 이유로 fault-tolerant하지 않다. 예를 들어, console모드는 fault-tolerant하지 않다.
        - 실패한 task에 대해서는 checkpoint로부터 직접 다시 시작해줘야한다.
        - Structured Streaming이 지원하지 않거나 개념상 적용되지 않는 기능들도 꽤 있다.
            - sorting 은 agg이후 complete output모드에서만 적용할 수 있다.
            - Distinct operations 기능은 지원하지 않는다.
            - Limit 기능은 지원하지 않는다.
            - Multiple streaming aggregations (agg을 연속적으로 하는 것)은 아직 구현되지 않았다.
            - 일부 join 기능은 지원하지 않는다. (e.x. Full Outer Join)
            - count() 를 단독으로는 못 쓴다. 대신, DS.groupBy().count()를 사용할 것.
            - show() 대신 console sink를 사용할 것.
            - foreach() 대신 DS.writeStream.foreach()를 사용할 것.
        - 특징
    - **Stateful Operation**
        - Structured Streaming에서 State란?
            - Spark Streaming에서와 마찬가지로 이전 배치들을 통해 나온 중간 데이터이다.
            - 데이터의 누적, 중복 제거 등의 상황에서 이전 데이터들의 처리 정보인 state가 필요하다.
        - EventTime aggregation
            - EventTime에 대해 aggregation을 하는 것. window연산도 포함.
        - mapGroupsWithState and flatMapGroupsWithState function. (아직 실험적인 단계)
            - groupByKey로 만들어진 Key-Value 그룹 데이터에 이 함수를 적용할 수 있다.
            - 배치별로 사용자 지정 state를 유지하면서 각 key의 group마다 사용자 지정 함수를 적용하는 방식이다.
            - EventTime이나 ProcessingTime에 대해서 timeout을 적용한다. timeout을 설정 안 할 수도 있다.
            - mapGroupsWithState와 flatMapGroupsWithState의 차이점로는 전자는 1개의 기록(값)만 반환가능하지만 후자는 여러개를 반환할 수 있으며, 적용할 수 있는 output Mode도 다르다.
                - mapGroupsWithState의 반환 값은 Dataset으로 변환되어 반환된다. output Mode는 append만 가능.
                - flatMapGroupsWithState의 반환 값은 Iterator로, Iterator의 각 값들이 row가 되어 마찬가지로 Dataset이 반환된다. outputmode는 append, update가 가능.
