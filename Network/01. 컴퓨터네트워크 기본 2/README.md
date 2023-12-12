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
