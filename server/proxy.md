# Proxy

- **proxy 란** : 클라이언트가 자신을 통해서 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해 주는 컴퓨터 시스템이나 응용 프로그램 (중계기)

- **reverse proxy** : 클라이언트 호출 시, 리버스 프록시 호출. 그리고 이 리버스 프록시 서버가 웹서버 호출 (일종의 소프트웨어 기반 로드 밸런서 역할도 가능) [1]
  - 성능 (로드 밸런싱 & 캐시 사용) [2][3]
  - 보안에 좋음 (SSL 암호화)
  - ex) NginX
- **upstream & downstream** : 데이터(패킷)를 보내는 쪽이 upstream, 받는 쪽이 downstream

- http keep alive 설정? : 원래 http 는 데이터를 받은 후, connection을 끊어버림. 이걸 계속 연결 시킴 [2]

## 참조
[1] https://www.joinc.co.kr/w/man/12/proxy
[2] https://developer88.tistory.com/299
[3] https://losskatsu.github.io/it-infra/reverse-proxy/#2-%ED%8F%AC%EC%9B%8C%EB%93%9C-%ED%94%84%EB%A1%9D%EC%8B%9Cforward-%EC%84%9C%EB%B2%84%EB%9E%80
[4] https://sujinhope.github.io/2021/06/13/Network-%ED%94%84%EB%A1%9D%EC%8B%9C(Proxy)%EB%9E%80,-Forward-Proxy%EC%99%80-Reverse-Proxy.html
