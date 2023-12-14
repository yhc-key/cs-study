# Application Layer(2)

Tags: Network

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled.png)

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%201.png)

# Transport - TCP의 Data Transfer

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%202.png)

- **Reliable**한 Data Transfer : 데이터가 하나도 유실되지 않는 것
    - RDT protocol(Reliable Data Transfer protocol)
- Network Layer에서 일어나는 Transfer → Unreliable
    - Message error
    - Message loss

## 상황설정) final state machine

### RDT 1.0

- 가정
    - underlying channel이 완벽하게 reliable함(현실에선 불가능)

⇒ transfer는 할 게 없음

### RDT 2.0

- 가정
    - 패킷 에러가 가능한 채널
- 에러 해결을 위한 mechanisms
    - Error detection
        - checksum을 붙여서 보내면 됨
    - Feedback
        - **Acknowledgements(ACKs) :** 에러없이 잘 왔다고 피드백함
        - **Negative acknowledgements(NAKs) :** 에러가 있다고 피드백
    - Retransmission
        - 재전송

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%203.png)

<aside>
💡 이 동작은 완벽하지 않다!

</aside>

![ACK feedback이 error가 나는 경우는 완벽하게 해결되지 않음](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%204.png)

ACK feedback이 error가 나는 경우는 완벽하게 해결되지 않음

- ACK error가 나서 Receiver checksum이 에러로 판단.
- 리시버 입장에서는 error난 패킷을 받은거니까 이전에 제대로 된 패킷을 받았는지 알 수 없어 재전송을 함
- 그러면 중복된 packets을 받게 됨. → 새로운 패킷인지 중복된 패킷인지 구별을 못함

### [RDT 2.1] Packet error 해결

⇒ **Sequence Number를 각 패킷의 header에다가 붙인다!**

- sequence number를 최소화시켜야함
    - 이 예제에서는 번호 0,1만 있어도 됨
        
        ![이런식으로 0,1만 있어도 됨](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%205.png)
        
        이런식으로 0,1만 있어도 됨
        

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%206.png)

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%207.png)

### [RDT 2.2] NAK-free protocol

- NAK를 사용하지 않고 ACK만으로 에러를 알 수 있음
- ACK를 보낼 때 무조건 Sequence Number와 함께 보냄
- 만약 Sender가 0을 보내서 Receiver가 다시 ACK(0)이런 식으로 받은 번호를 보내면 에러가 나지 않은 것
- 만약 Sender가 1을 보냈는데 ACK(0)이런 식으로 다른 번호가 오면 에러가 난 것.
- 그러면 재전송 함

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%208.png)

## RDT 3.0

- 가정
    - error와 유실이 있는 상황
    - 가장 현실적인 상황
- packet loss를 위한 mechanisms
    - **Timer**
        - 타이머를 설정해서 정한 시간 내에 아무 대답이 없으면 재전송
        - 시간은 어느정도로 정해야 할까?
            - 적당히 기다림
            - Trade off가 있어서 시간을 정하기 어려움
                - 짧으면 반응은 빠른데 오버헤드가 됨
                - 느리면 loss 극복이 느림
    
    ![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%209.png)
    

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%2010.png)

![d이후의 상황을 생각해보기](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%2011.png)

d이후의 상황을 생각해보기

## 정리

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%2012.png)

- Error detection, feedback, retransmission, sequence number 등등의 field가 packet의 header안에 있음.
- 지금 이 방식은 한번에 하나씩만 패킷을 보내서 현실적이지 않음
- 현실의 TCP는 한번에 패킷을 여러개를 보내는 pipelined protocol을 사용함

![Untitled](Application%20Layer(2)%202264a0b5a6c64c48918a9de5bdf23de5/Untitled%2013.png)