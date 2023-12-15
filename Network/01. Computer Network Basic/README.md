# 01. 컴퓨터네트워크 기본 1

# ✨INTRO✨

## 📌 네트워크 구조 형태

### ✔️ 가장자리(network edge)

- 응용프로그램(application) 및 호스트(hosts)

- 웹 서버와 같은 서버들

### ✔️ 가운데(network core)

- 라우터(routers)
  - 메세지를 전달 받아서 목적지를 향해 전달해 주는 역할

### ✔️ 연결

- 이러한 구성 요소들을 연결 해주는 링크들(communication links)
  - ex) 무선 링크(wifi)
  - 링크 종류는 다양함

# ✨Network edge✨

### ✔️ hosts(end systems)

- 프로그램 실행 응용 프로그램들
- ex) Web, email

### ✔️ client/server model

- client: 자기가 원할 때 링크로 연결을 해서 서버로 연결 받아 옴
- server: 항시 연결되어 있어서 클라이언트로부터 들어올 요청 기다림

### ✔️ peer-peer model

## 📌 데이터 통신 서비스 - Connection-oriented service

### ✔️ TCP: Connection-oriented service를 제공하는 통신 방법

❗신뢰성이 필요한 데이터 보낼 때 사용❗

- 전송 제어 프로토콜(Transmission Control Protocol)

- `Reliable(신뢰성 있는), in-order(순서) byte-stream data transfer`
  - 내가 보낸 메세지 그대로, 내가 보낸 순서 그대로 감
- `flow control` - 흐름 제어

  - sender가 보낸 속도를 조절해줌
  - receiver의 능력에 맞춰서 속도를 전달

- `congestion control` - 혼잡 제어

  - 중간에 네트워크 상황에 맞춰서 조절

- 🔑 사용하는 곳
  - 웹 브라우저 같은 곳 (ex- 네이버의 경우 뉴스를 보여줘하는데 데이터 유실 되면 안 됨)
  - HTTP(Web)
  - FTP(file transfer)
  - Telnet(remote login)
  - SMTP(email)

### ✔️ UDP: Connection-oriented service를 제공하는 통신 방법

❗신뢰성이 필요 없는 데이터 보낼 때 사용❗

- 사용자 데이터그램 프로토콜(User Datagram Protocol)
- `connectionless`, `unreliable data transfer`, `no flow control`, `no congestion control`

  - 아무것도 안 해줌!
  - 데이터 유실되든, 데이터 터져 나가든~ 상관 없을 때 사용

- 보내는 입장에서 자기 마음대로 보낼 수 있다는 것이 장점
- 도착한다는 보장이 없다는 것이 단점

- 🔑 사용하는 곳
  - 실시간 전화 같은 곳 (ex- 전화 보내는 데이터 몇 개 유실 되어도 괜찮음)
  - streaming media
  - teleconferencing
  - DNS
  - Internet telephony

## 📌 프로토콜(Protocol)

### ✔️ 프로토콜이란?

- 전달자와 수신자 사이의 암묵적인 방식, 약속
- 정말 중요한 메세지를 주고 받기 위한 서로간의 준비 동작과 같음
- ex) 대화 시작 - 잘 지냈니?

- 인터넷의 모든 통신은 프로토콜에 의해 통합됨

# ✨Network Core✨

- 라우터들의 집합
- 과연 어떤 방식으로 출발지에서 도착지까지 라우터가 전달하냐?

## 📌 메세지 전달 방식

### ✔️ circuit switching (회선 교환)

- 출발지에서 목적지까지 가는 길을 미리 예약한 뒤, 특정 사용자만 사용하도록 만든 것
- ex) 유선 전화망

### ✔️ packet-switching (패킷 교환)

- **`인터넷에서 사용하는 방식`**
- 유저가 보내는 메세지를 패킷 단위로 받아서 그때 그때 올바른 방향으로 보내는 것
- ex) A, B 순서로 한 라우터에 들어온다면 들어온 순서대로 목적지에 각자 보내줌

## 📌 **circuit switching** vs **packet-switching**

<div align='center'>
    <img src="img/01-1.png" width="500px">
</div>

#### <가정>

- 가운데 회색원: 라우터
- 왼쪽 네모: 각각의 유저
- 1 Mbps link: 라우터에서 나가는 링크
  - 1Mbps: 초당 1 메가 비트의 크기의 데이터를 한꺼번에 보낼 수 있는 케이블 (link bandwidth)
- 100Kbps/sec 속도로 데이터를 보낸다고 한다면

### ✔️ circuit switching (회선 교환) 방식의 경우

- 최대 10명의 유저까지 수용할 수 있음
  - 11명 안 됨

### ✔️ packet-switching (패킷 교환) 방식의 경우

- 몇 명까지 가능한 개념이 없음
  - 제약이 없음

❗ 그런데 10명 이상 몰리면?

- 실제로 사용자들이 인터넷 사용하는 패턴
  - 웹 서버에서 데이터를 보내면 유저가 계속해서 서버와 상호작용하지 사용하지 않음
  - ex) 유저가 네이버 뉴스를 본다고 했을 때 뉴스를 클릭하면 웹에서 데이터를 보내준뒤 사용자는 계속해서 그 데이터를 클릭하거나 상호작용하지 않고 보낸 데이터를 확인하고 읽는 시간이 있음
    - 사실상 데이터가 사용되지 않는 시간
- 10명이 정확히 동시에 몰리지 않는 이상 데이터 요청이 분산되어서 문제가 생기지 않음

## 📌 packet-switching 단점

<div align='center'>
    <img src="img/01-2.png" width="500px">
</div>

### ✔️ processing delay

- 패킷을 받았을 때, 라우터는 이것이 패킷인지, 에러가 없는지 검사를 함
- 그리고 어디로 보내야 하는지(목적지) 확인해서 보내는 일을 해야 하는데 이때 딜레이가 생기고, 이것이 `processing delay`

### ✔️ queueing delay

- 전송되는 속도보다 유저가 몰리는 속도가 더 높다면 줄이 생김
- 즉, 나가는 속도보다 들어오는 속도가 더 빠를 때, 라우터 안의 queue에 임시 저장함
- 이때 각각의 패킷은 자기 순서가 올 때까지 기다려야 하는데 이렇게 기다리는 시간이 `queueing delay`

### ✔️ Transmission delay

- queue 제일 앞에 서 있다가 나갈 때 걸리는 딜레이
- 패킷 속의 첫 번째 비트가 링크로 올라가는 시간부터, 마지막 비트가 링크로 올라갈 때까지 걸리는 시간
- `패킷 크기/link bandwidth`
  - link bandwidth가 작으면 작을수록 Transmission delay가 커짐

### ✔️ Propagation delay

- 한 패킷의 마지막 비트가 링크에 올라와서 다음 라우터까지 도달할 때까지 걸리는 시간
- 속도는 단순히 빛의 속도
- 물리적인 링크의 길이/빛의 속도
  - 단순히 물리적인 링크의 길이가 길면 오래 걸리고, 작으면 적게 걸림
  - 빛의 속도는 건드릴 수 없기 때문

## 📌 packet-switching의 딜레이 줄이는 법

### ✔️ processing delay

- 라우터 성능을 개선

### ✔️ queueing delay

- 제일 골치 아픈 애임
- queue는 조절할 수 없음
  - 사람이 몰리냐 안 몰리냐에 따른 것이기 때문에
- 네트워크 상에서 발생하는 가장 흔한 딜레이임
- 그런데 queue의 크기가 한계가 있음
  - 큐가 넘치는 상황이 되면 패킷 유실이 됨
  - 패킷 유실의 90% 이상은 queueing delay에서 생김

#### ❗TCP는 reliable인데 queueing delay로 패킷 유실이 되면? ❗

- 최악의 상황일 경우 유실은 일어나기 마련이라 재전송해야 함

1. 직접 라우터가 재전송하는 법
2. 아예 처음부터 재전송하는 법
   - TCP는 이 방법을 사용
   - 네트워크 엣지는 지능적임(intelligent), 네트워크 코어는 전달만 함(Dumb Core)

### ✔️ Transmission delay

- link bandwidth 늘리기

### ✔️ Propagation delay

- 신의 영역(빛의 속도 빠르게 할 수 없음)

<br>

# 01. 컴퓨터네트워크 기본 2

# ✨Caravan analogy✨

> packet-switching의 딜레이를 카라반에 비유한 예시

<div align='center'>
    <img src="img/컴퓨터네트워크 기본2-01.png" width="500px">
</div>

### ✔️ 설명

차량 10대가 항상 한꺼번에 다녀야 하는 상황

- 차랑 1대 = bit
- 차량 10대 = 패킷

중간에 톨게이트가 2개

- 톨게이트 = 라우터

톨게이트 사이의 거리는 100km

차량 1대가 진행하는 속도는 100km/hr

- Propagation delay

톨게이트는 차량 1대 넘어갈 때 12초

- Transmission delay

❗여기서 차량 10대가 두 번째 라우터까지 도달하는데 걸리는 딜레이는 얼마?

- (processing delay, queueing delay는 없음)
- 10개의 차량이 톨게이트 통과할 때까지 걸리는 시간: 10 x 12 (초) = 120초
- 마지막 차량이 다음 톨게이트에 도착하는데 걸리는 시간: 100km/(100km/hr) = 1시간
- 정답은 1시간 + 120초 = 62분

### ✔️ 여기서 중요한 것

1. 12초 뒤라면
   - 1번째 비트는 이미 통과했음
   - 실제로 Propagation delay는 빛의 속도이기 때문에 링크로 뿜어져 나오는 순간 다음 라우터에 도착함
2. 그런데 여기서 문제는 모든 패킷이 다 도착해야 다음 라우터에서 진행함
   - 이것이 packet switching이라는 걸 알면 됨

# ✨네트워크 개요✨

| 컴퓨터 네트워크 계층 구조 | 중요한 프로토콜        |
| ------------------------- | ---------------------- |
| Application Layer         | HTTP                   |
| Transport Layer           | TCP, UDP               |
| Network Layer             | IP                     |
| Link Layer                | WIFI, LTE/3G, Ethernet |

각 계층마다 다양한 프로토콜이 존재함

## 📌 Network apps (네트워크 애플리케이션)

애플리케이션, 프로그램

네트워크 기능이 있는 프로세스를 의미

ex) e-mail, web, text messaging, remote login 등

### ✔️ Application Layer에서 프로토콜은?

서로 반대편에 있는 Network Edge들 사이의 통신

- 프로세스와 프로세스 사이의 통신 (ex. 웹 브라우저 - 웹 서버)
- 중간의 네트워크는 신경 쓸 필요 X
- 라우터에는 Network Layer까지만 존재함 (Network, Link) - 바보들

## 📌 Client-Server 구조

### ✔️ Server

- 웹 서버
- 항상 24시간 동작해야 함
- 영구적인(permanent) IP 주소
  - IP 주소: 인터넷 상의 존재하는 모든 컴퓨터들이 가지는 각자의 주소
  - 서버는 바뀌지 않고 고정된 IP 주소를 가지고 있어야 함
  - 서버의 주소가 고정되어 있어야 사람들이 찾아가기 때문

### ✔️ Client

- 웹 브라우저
- IP 주소가 고정적이지 않아도, 바뀌어도 됨

## 📌 Processes communicating

### ✔️ 프로세스

- 호스트(host) 내에서 실행 중인 프로그램

OS의 프로세스 간 통신(inter process communication)과 동일

## 📌 Socket

### ✔️ Socket이란?

> 프로세스가 네트워크 세계로 데이터를 내보내거나, 데이터를 받기 위한 실제적인 창구 역할을 하는 것
>
> 프로세스가 데이터를 보내거나 받기 위해서 반드시 소켓을 열어서 소켓에 데이터를 써보내거나 소켓으로부터 데이터를 읽어와야 함

### ✔️ 프로세스와 프로세스를 연결하려면?

출발 지점 프로세스는 도착 지점 프로세스의 Socket 주소를 알아야 함

- 이 Socket을 인덱싱하는 것이 필요
- IP 주소, Port 번호의 결합
  - Port: 한 랩탑에서 돌아가는 프로세스 중 하나의 프로세스를 지칭하는 번호

ex) 만약 개인 노트북으로 네이버에 접속하고 싶다면?

웹 브라우저(client) => 웹 서버(네이버)

웹 브라우저에서 `www.naver.com`을 입력함

- 사실은 IP주소와 Port 번호를 입력해야 함
- 하지만 사람들을 위해서 DNS 시스템을 이용하여 `www.naver.com` 주소를 IP주소&Port번호로 변환시킴

- 거의 모든 웹서버는 Port 번호를 80을 씀
  - Why? - client가 server를 찾아가기 쉽게 하기 위해 암묵적 약속

## 📌 계층이란 개념에 대해서

하위 계층에서 상위 계층에게 어떠한 기능(서비스)을 제공하는 것

Ex) Application 계층의 경우 바로 하위 계층인 Transport 계층에서 제공하는 기능(서비스)을 사용함

그렇다면

### ✔️ (Applicatoin 개발자 입장에서) Transport 계층에서 제공해줬으면 하는 희망사항?

1. **데이터 무결성(data integrity)**

   내가 보낸 데이터가 유실되지 않고 목적지까지 온전하게 도착했으면 좋겠다

2. **타이밍(timing) (시간 제약)**

   내가 보낸 데이터가 ~ 전까지 도착했으면 좋겠다

3. **처리량(throughput)**

   내가 보낸 데이터가 ~ 이상 용량이 나왔으면 좋겠다

4. **안전성(security)**

   내가 보낸 데이터가 안전했으면 좋겠다

이 4가지 중 Trasnport 계층에서 제공하는 서비스는 1번 데이터 무결성만 제공해줌(TCP에서) (UDP는 이것도 제공 안 함)

#### _Q. timing과 throughput이 같은 얘기 아닌가?_

- 아님!!!!

- timing은 정해진 범위 안에 도착해라는 얘기, throughput은 1초에 어느 정도의 양이 도착해야 한다는 얘기

- 만약 100개를 10초 안에 보내라는 시간 제약이 주어지면 9초까지 0개를 보냈다가 10초 되기 직전에 100개를 다 보내도 timing은 문제 없음

- 그러나 1초에 10개가 도착해야 한다는 throughput이 정해져 있다면 이 경우 문제가 있음

## 📌 Application 유형별 application 계층의 protocol과 transport 계층의 protocol

<div align='center'>
    <img src="img/컴퓨터네트워크 기본2-02.png" width="500px">
</div>

# ✨HTTP✨

## 📌 HTTP란?

**`Hypertext Transfer Protocol`**

Hypertext

- 텍스트인데 중간 중간에 다른 텍스트를 지칭하는 링크들이 있음

이런 텍스트를 전송하는 프로토콜이 HTTP

<img src="img/컴퓨터네트워크 기본2-03.png" width="300px">

- 종류가 딱 request, response 두 가지 밖에 없다
- request에는 client가 원하는 하이퍼텍스트 파일 이름이 있고, server는 이 파일을 읽어서 response에 담아서 줌

## 📌 TCP 사용

- HTTP는 Application 계층의 프로토콜이기 때문에, Transport 계층의 서비스를 사용함
- 그 중에서 HTTP는 TCP를 사용한다
- 따라서 HTTP의 Request, Response 이전에 TCP connection(연결)을 해 줘야 함
- TCP connection에 대해서는 이후에 배울 예정

## 📌 stateless(상태 없음)

- HTTP는 요청이 들어오면 해당 내용을 담아서 응답 처리한 후, 다른 요청을 처리하고...
- 상대방에 대해 저장하지 않음

## 📌 HTTP connections

TCP connection을 지속적으로 사용하냐마냐로 두 가지로 나뉨

HTTP의 request, response를 사용하기 위해서는 network edge(client, server)가 서로 TCP connection이 되어 있어야 함

이때, TCP 연결 후 HTTP를 통해 사용자 요청 처리 후 응답 과정이 끝난 후 TCP 처리가 다름

### ✔️ non-persistent HTTP

- request, response 한 뒤 TCP 연결 끊음

### ✔️ persistent HTTP

- request, response 한 뒤 TCP 연결 끊지 않고 유지하면서 재사용
- 웹브라우저가 디폴트로 사용하는 방식

## 📌 non-persistent vs persistent 예시

`www.someScholl.edu/someDepartment/home.index`라는 URL에 들어가려고 할때

- 텍스트와 10개의 이미지를 참조하고 있는 페이지임

### ✔️ non-persistent HTTP

1. Web브라우저에서 `TCP 연결`
2. 하이퍼텍스트 파일(home.index)에 대한 요청(request) 후 응답(response) 받음
3. `TCP 끊음`
4. home.index 파일 내 이미지를 가져오기 위해서는 해당 이미지 파일을 다시 요청(request)해야 함
5. `TCP 다시 연결`
6. 한 개의 그림 파일에 대해 요청 후 응답
7. `TCP 끊음`

4번부터 7번까지 10번 반복

### ✔️ persistent HTTP

1. Web브라우저에서 `TCP 연결`
2. 하이퍼텍스트 파일(home.index)에 대한 요청(request) 후 응답(response) 받음
3. home.index 파일 내 이미지를 가져오기 위해 해당 이미지 파일을 다시 요청(request) 후 응답
4. 3번을 10번 반복 후 `TCP 끊음`
