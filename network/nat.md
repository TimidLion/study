## NAT (Network Address Translation)

- **정의** : 라우터를 통한 통신 과정에서 IP 패킷의 헤더를 이용해 다른 IP 주소와 맵핑해 트래픽을 주고 받는 기술 [1][3]
- **왜 쓰는가?** : IPv4 주소 부족 문제를 해결하기 위한 방법으로, NAT는 사설(local)망과 외부 공인(public)망 간의 통신을 위한 네트워크 주소를 변환한다. [2]
- **장점** : IP 주소 부족 문제 해결 & firewall 설치로 사용자 사설 네트워크 보호 (보안!)
- **댠점** : 

### NAT 기술 [3]
- local IP <-> public(global) IP 1:1 연결
- translation tables 을 통해서 여러 private 네트워크(다) <-> 1 개의 public IP 주소를 연결
- local IP 와 public IP를 특정 TCP port 에 할당해서 연결
- public IP 연결 시도 시, round-robin 기반으로 local IP 와 연결

## 참조
- [1] https://docs.microsoft.com/ko-kr/azure/rtos/netx-duo/netx-duo-nat/chapter1
- [2] https://jwprogramming.tistory.com/30
- [3] https://www.techtarget.com/searchnetworking/definition/Network-Address-Translation-NAT
