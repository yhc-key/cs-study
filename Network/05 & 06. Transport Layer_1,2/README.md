# Transport Layer(전송계층)

- [Transport Layer(전송계층)](#transport-layer전송계층)
- [Pipelined Protocols](#pipelined-protocols)
  - [Go-Back-N](#go-back-n)
  - [selective repeat](#selective-repeat)
- [정리](#정리)
- [TCP segment structure](#tcp-segment-structure)
  - [Timeout](#timeout)
  - [TCP Reliable data transfer](#tcp-reliable-data-transfer)
  - [TCP 권고사항](#tcp-권고사항)

# Pipelined Protocols

![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled.png>)

## Go-Back-N

![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 1.png>)

- Window size
  - 한번에 얼마만큼의 packets을 보낼 것인지
  - window안에 있는 애들은 buffer안에 저장해야함
- 여기서의 ACK 성격
  - cumulative ACK : 쌓아놓는다는 소리.
    - ACK 11이면 한번에 11까지 받았다는 것.
    - 가장 먼저 보내는 패킷 send base에 timer가 있음
      - e.g. window size가 4면 0,1,2,3이 보내질 때 0한테 timer가 있다는 뜻
        ![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 2.png>)
- Receiver
  - 번호 순서대로 sequence number를 처리함.
    - sender한테 0을 받았으면 그 다음은 무조건 1받을 때까지 기다림. 만약에 2가 더 먼저오면 그거는 버리고 1이 올때까지 기다린다.
      ![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 3.png>)
- 만약 window size가 4고 중간에 6이 유실됨
  - 6이 send base일 때 6에서 터지니까 다시 4개가 돌고서 6으로 Go-Back하는 거임

## selective repeat

- 문제가 있을 때 재전송 해주지만 선택된 패킷만 재전송
  - 잘 전송된 패킷은 buffer에 저장해주고 타이머 해제됨
  - 2가 터지고 3은 전송이 잘됐으면 전송을 ACK3이런 식으로 전송해줌
  - 그리고 2는 재전송
  - 2가 잘 받아져서 2,3,4,5가 다 받아지면 패킷을 application layer로 전송
  - 그리고 그 다음 window로 넘어감
    ![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 4.png>)

<aside>
💡 **딜레마**

</aside>

- window size가 3이면 sequence number가 0,1,2,3을 쓴다고 가정(window size : N, sequence number : N+1)
- 근데 만약에 첫번째 패킷의 모든 애들이 에러가 남(0,1,2) → 3 대기중
- 그러면 재전송을 0부터 다시함 → sequence #는 0
- 리시버는 3이 대기하고 있으니까 3다음 번호로 0을 넣어줌
  - 중복패킷이 아닌 새로운 패킷으로 인식해버린다.

⇒ 해결책 : sequence number 범위를 늘리면 해결됨

- 얼만큼으로?

![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 5.png>)

---

# 정리

- point-to-point
  - 한쌍의 소켓의 통신을 책임짐
- reliable, in-order byte stream
  - 하나도 유실되지 않고 순서대로 간다
- pipelined
  - 한꺼번에 쏟아붓는다.
- send & received buffers
  - 누가 sender고 receiver고 나눠져있지 않음
- full duplex data
  - 데이터가 양방향으로 진행한다는 얘기
- connection-oriented
- flow controlled
  - receiver의 용량만큼 부어주는거
- condition controller

![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 6.png>)

# TCP segment structure

![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 7.png>)

![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 8.png>)

- 각 포트가 16bit(포트 번호 범위 0~2^16-1)

![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 9.png>)

<aside>
💡 A가 B한테 ‘C’를 보내려고 하는 상황

</aside>

- **시퀀스 번호**
  - 바이트 순서 번호
- **ACKs**
  - cumulative ACK
  - ACK10→ 9번까지 받았으니까 이제 10 주라는 뜻(Go-Back하고 의미 다름)
  - B가 보내는게 에코인지 아닌지 구별 불가

## Timeout

- 시간제한을 어떻게 정할까?

  - RTT (Round Trip Time) : segment를 보내고 돌아오는 시간
    - 시간 제한을 RTT로 정해보자
      - 경로마다 RTT값이 다름(거리마다 다르고, Queue delay가 다름)→RTT를 기준으로 잡기가 중구난방이라 힘들다.
  - Estimated RTT : 보정된 RTT값
    - 너무 타이트할 때도 있음
      ![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 10.png>)
  - Dev RTT : Estimated RTT에 시간을 더해줌

  ## TCP Reliable data transfer

  - timer 1개 사용
    - 타이머가 터지면 딱 그거 하나만 재전송
      ![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 11.png>)

  1. 상황

     segment 1 : 92~99

     segment 2 : 100~119

     - B는 둘 다 잘 받아서 ACK 120기다림
     - sender가 response기다리던 중에 1번 세그먼트가 timer가 터짐
     - 1번 재전송해서 120번 기다리는 리시버에게 날라가면 버려짐
     - 그리고 ACK120번이 B에서 A로 날라감
     - 그러면 119까지는 잘 처리된거니까 A 비움
       ![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 12.png>)

  1. 상황
     - 2와 거의 동일한데 1번 세그먼트가 유실된 상황
     - 2와 비슷하게 동작
       ![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 13.png>)
       ![Untitled](<Transport Layer(전송계층) 2f4789b102664cbda0ad696c6395d8ed/Untitled 14.png>)

## TCP 권고사항

- delay를 해라 → 한번에 다 담아서 보내라고
- **fast retransmit :** timer가 터지기 전에 유실을 알 수 있음
  - 특정번호의 ACK가 계속 들어오면 유실인것임 → 동일 번호가 4번이상 반복되면 유실로 판단해줌⇒ **3 duplicate ACK**
  - 없어도 되는 기능. 필수는 아님
