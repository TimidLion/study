# kafka serialize & deserialize 
- **serialize** : object -> byte stream [2]
- **deserialize** : byte stream -> object [2]

## Kafka 직렬화 & 역직렬화 방식
<img width="1000" alt="image" src="https://user-images.githubusercontent.com/10977644/187843305-f1994c81-13c3-441c-a601-595a55aa540f.png">

### Kafka 메세지 Consume 과정
1. 직렬화된 데이터를 브로커에서 받아옴.
2. 받아온 데이터를 역직렬화 진행
3. 역직렬화한 데이터를 consume
4. 브로커에게 commit 신호 전송

#### 주의해야하는 부분 [1]
- 3번 과정 (consume) 에서 실패한 경우, 최대 횟수만큼 retry 후 commit 신호를 보냄.
  - 이 경우, 브로커에서는 메세지를 제대로 보냈다고 표시 (받는 쪽에서 실패한 것)
- 2번 과정에서 실패한 경우, 계속 반복하는 문제 발생

- 해결법 : Spring Kafka 2.2 이후 도입된, `ErrorHandlingDeserializer`를 사용해 `ErrorHandler` 지정


## 참조
- [1] http://homoefficio.github.io/2022/08/28/Kafka-Poison-Pill-Spring-ErrorHandlingDeserializer/
- [2] https://www.geeksforgeeks.org/serialization-in-java/
- [3] https://www.baeldung.com/kafka-custom-serializer
