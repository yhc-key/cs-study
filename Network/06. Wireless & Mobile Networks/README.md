- [Wireless and Mobile Networks](#wireless-and-mobile-networks)
- [Wireless](#wireless)
  - [IEEE 802.11 (Wireless Fidelity : WIFI)](#ieee-80211-wireless-fidelity--wifi)
    - [802.11 Lan architecture](#80211-lan-architecture)
    - [multiple access](#multiple-access)
    - [CSMA/CA](#csmaca)
      - [CSMA/CD와의 동작 차이](#csmacd와의-동작-차이)
    - [RTS-CTS](#rts-cts)
    - [frame : addressing](#frame--addressing)
    - [frame : more](#frame--more)
  - [Cellular Internet access](#cellular-internet-access)
    - [MAC Protocol](#mac-protocol)
- [Mobility](#mobility)
  - [용어](#용어)
    - [Registration](#registration)

# Wireless and Mobile Networks

# Wireless

우리가 보는 대부분의 무선 네트워크 : infrastructure이 있고, single hop인 경우

무선의 신호 세기

- 거리가 멀어지면 떨어짐 → 보호를 받지 못해 간섭이 생기기 때문
  - 문제점 : Hidden Terminal Problem
    - A가 B한테 전송하고 있는지 아닌지를 C는 모름. 그러면 언제 시그널을 보내야하는지 모름
  - Collision Detection 불가 : 내 신호는 크게 들리고 주변의 신호는 작게 들림
  ![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled.png)

## IEEE 802.11 (Wireless Fidelity : WIFI)

### 802.11 Lan architecture

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%201.png)

- AP(=BSS)
  - **passive scanning**
    - ap가 주기적으로 자신의 ap정보를 beacon에 담아서 broadcast함
    - 시그널 세기가 좋은 것을 골라 연결함
- switch
- router

### multiple access

WIFI 특징 : 충돌이 발생하는데 충돌 감지가 안됨;

⇒ ACK 필요(유선은 필요 없음)→TCP와 전혀 다른 ACK임!!

- 충돌이 있는지 없는지 알려주는 역할을 함

### CSMA/CA

- Sender
  - sender가 receiver가 신호를 받고 있는지 확인하고 조용하면 보냄
  - 보내고서 ACK가 오면 제대로 갔다는 것을 확인
- Receiver
  - channel이 조용하면 SIFS(기다리는 시간)만큼 기다리고 ACK를 보냄

#### CSMA/CD와의 동작 차이

유선 : 보낼 때 충돌 감지하면 바로 멈춤

무선 : 충돌 감지를 못함. 충돌이 있어도 계속 보냄

→ 무선에서 발생한 충돌의 피해가 큼

### RTS-CTS

무선에서 충돌이 났을 때 생기는 문제를 해결하기 위한 프레임.

RTS : Ready To Send

- 데이터 보내기전에 주변에 알리는 컨트롤 프레임
- 메타 데이터로 채널 사용시간과 데이터 크기를 담고 있음
- AP통신 반경에 있는 것들도 동일한 정보를 공유받음→다른 곳에서 이 시간동안 데이터를 보내지 않음

CTS : Clear To Send

- 데이터 보내는 것을 허락해주는 컨트롤 프레임

1. AP에 충돌이 나는 것을 확인하기 위해 RTS를 우선 전송함
2. 충돌이 나서 가만히 있느라 CTS를 반환하지 못함
3. CTS가 오지 않아 충돌난 것을 알게됨
4. 다시 RTS를 보냄
5. 충돌이 나지 않아 CTS를 보냄
6. 진짜 데이터 안전하게 전송

<aside>
💡 만약 a의 CTS가 b에게 가는 중에 b가 RTS를 보냈다면?

</aside>

1. b주변의 애들은 노이즈를 듣게 됨→ 아무것도 못들음
2. A는 CTS를 받았으니 데이터 AP에게 전송
3. B는 A가 채널 예약한 것을 모름
4. B는 RTS보냄→A의 데이터 노이즈 남→ 프레임 다 날라감(data collision)
5. ACK안가서 다시 RTS 둘 다 보냄
6. 버려진 프레임은 TCP에서 재전송됨

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%202.png)

### frame : addressing

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%203.png)

- address 1 : 무선 프레임을 받는 인터페이스 주소
- address 2 : 프레임 전송 인터페이스 주소
- address 3 : 담긴 아이피 패킷을 처리할 MAC 주소

(4는 무시)

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%204.png)

AP의 특징 : 유선 라우터 입장에서는 MAC이 보이지 않음

- address1 : AP의 MAC 주소(도착지를 위해 필요)
  - DHCP로 내 IP 알아냄
  - broadcast request를 ap에 보냄
  - beacon message에서 알게됨
- address2 : 전송자 MAC 주소
- address3 : 라우터 MAC 주소(IP정보를 AP가 해독할 수 없어서 AP가 라우터로 정보를 보내야해서 필요)
  - ARP 테이블 참조 → IP : 게이트웨이, MAC : ?
  - ARP 쿼리를 보내서 라우터 MAC 주소 알아냄

집에 있는 무선 공유기는 AP+라우터 형태임

이더넷 필드

- source 주소 : h1 MAC 주소 → 스위치와 느낌 비슷

### frame : more

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%205.png)

WIFI 프레임 종류 : RTS, CTS, ACK, data

duration : RTS, CTS 사용할 때 얼마나 사용할 지 적어놓음

- Connection이 유지 되어야 함
  - source ip address / port, destination ip address / port 이 네가지가 유지돼야 connection 유지
  - 만약 AP를 이동하면 스위치 테이블만 바꾸면 됨→트래픽이 바뀐쪽으로 흘러감

## Cellular Internet access

### MAC Protocol

cell 안에서 유저와 기지국의 통신 방식

- 전통적 방식 → 채널 파티셔닝 : TDMA, TDMA
- 3G로 넘어오면서 CDMA 사용 → 각 유저한테 코드가 배분이 돼서 자기한테 오는 신호만 크게 증폭이 됨

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%206.png)

→ 데이터 전송 속도로 나눔

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%207.png)

- 이렇게 계층화돼 존재
- GGSN이 하위 계층에 ip주소를 줌

# Mobility

네트워크 관점에서의 모빌리티 : connectivity가 있는 상태에서 네트워크를 넘나드는 이동이 있는 상황

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%208.png)

- 낮은 mobility : 같은 곳에서 쓰고 끝
- 중간 : 다른 ip에 연결되면 연결이 안됨
- 높음 : network를 넘나들어도 connection유지됨

## 용어

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%209.png)

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%2010.png)

- permanent address : 어디를 가도 변하지 않는 주소
- home agent : 홈 네트워크가 어디 있는지 관리하는 거
- visited network : 움직인 네트워크
- care-of-address : 새로 접속한 네트워크에 속하면서 배정받는 주소
- foreign-agent
- correspondent : mobile network에 들어가고싶은 존재

### Registration

- **Indirect Routing**

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%2011.png)

1. 다른 네트워크로 사용자가 접속함
2. foreign agent가 home agent한테 자기 네트워크에 기기가 접속되어 있다고 연락을 함

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%2012.png)

1. 패킷을 홈으로 보내면 foreign으로 보냄
2. 그러면 거기로 연결함

- **Direct Routing**

![Untitled](Wireless%20and%20Mobile%20Networks%20acfc00dd053b492d973748165c85b1b6/Untitled%2013.png)

새로운 주소를 알려줘서 바로 연결을 함.

⇒ 이런 개념들은 현실에서는 존재하지 않음.
