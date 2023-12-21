# DB Connection이란?

DB를 사용하기 위해 DB와 애플리케이션 간 통신을 할 수 있는 수단이다

DB Connection은 Database Driver와 Database 연결 정보를 담은 URL을 필요로 한다

Java의 DB Connection은 JDBC를 주로 이용하며, URL 타입을 사용한다

<div align='center'>
    <img src="img/dbcp_1.jpg" width="550px">
</div>

## JDBC

JDBC(Java Database Connectivity)란 자바 언어로 다양한 관계형 데이터베이스(RDBMS, Relational Database Management System)에 접속하여 SQL문을 수행하여 처리하고자 할 때 사용되는 표준 SQL 인터페이스 API이다

즉, JDBC란 Java에서 DB에 접속할 수 있도록 하는 자바 API.

각각의 DB에는 연결 방식과 통신 규격이 따로 있기에 프로그램을 DB와 연결한다면, 해당 DB와 관련된 기술적 내용을 배우고 DB가 변경될 시 많은 변경 사항이 존재함.

그래서 JDBC라는 자바 표준이 생김.

<JDBC 장점>

- DB 변경할 때마다 DB 접근하는 코드를 수정하는 대신 추상화된 JDBC 인터페이스 의존하여 개발
- JDBC 표준 인터페이스만 학습하면 학습한 내용을 수십 개의 데이터베이스에 동일하게 적용 가능

즉, DBMS(MySQL, MsSQL, Oracle 등)에 관계없이 하나의 JDBC API만을 사용하여 데이터베이스 관련 작업을 처리할 수 있음.

<div align='center'>
    <img src="img/dbcp_2.png" width="550px">
</div>

자바 애플리케이션에서 데이터베이스에 접근하기 위해서는 JDBC API를 이용하여 접근해야하며, JDBC API는 JDBC 드라이버를 거쳐 데이터베이스와 통신함

### JDBC 드라이버란?

자바 프로그램 요청을 DBMS가 이해할 수 있는 프로토콜로 변환해 주는 클라이언트 사이드 어댑터이다

각각의 DBMS는 자신에게 알맞는 JDBC 드라이버를 제공함

### JDBC 실행과정

<div align='center'>
    <img src="img/dbcp_3.png" width="550px">
</div>

1. DB 벤더에 맞는 드라이버 로드

2. DB 서버의 IP,ID,PW 등을 DriverManager 클래스의 getConnection() 메소드를 사용하여 Connection 객체 생성

3. Connection에서 PreparedStatement 객체를 받음

4. executeQuery를 수행하고, ResultSet 객체를 받아서 데이터를 처리

5. 사용한 ResultSet, PreparedStatement, Connection을 close함

```
getConnection() :
DriverManager.getConnection() 메소드는 자바 프로그램과 데이터베이스를 네트워크 상에서 연결하는 메소드

연결에 성공 시 DB와의 연결상태를 Connection 객체로 표현하여 반환함

부하가 가장 많이 걸리는 과정

Connection :
DB 연결 객체

데이터베이스로의 연결 기능 제공, Statement 객체 생성 기능 제공

SQL문을 데이터베이스에 전송하거나, SQL문을 commit/rollback하는데 사용

보통 Connection 하나 당 트랜잭션 하나를 관리함

Statement/PreparedStatement :
SQL문 실행 객체

ResultSet :
쿼리문 결과 객체

Driver :
각 JDBC Driver 벤더에서 구현해야 하는 인터페이스

DriverManager :
사용될 드라이버를 등록, 등록 해제 기능과 DB와 연결 기능 수행

```

### Connection Pool

```
Connection conn = null;
PreparedStatement  pstmt = null;
ResultSet rs = null;

try {
    sql = "SELECT * FROM T_BOARD"

    // 1. 드라이버 연결 DB 커넥션 객체를 얻음
    connection = DriverManager.getConnection(DBURL, DBUSER, DBPASSWORD);

    // 2. 쿼리 수행을 위한 PreparedStatement 객체 생성
    pstmt = conn.createStatement();

    // 3. executeQuery: 쿼리 실행 후
    // ResultSet: DB 레코드 ResultSet에 객체에 담김
    rs = pstmt.executeQuery(sql);
    } catch (Exception e) {
    } finally {
        conn.close();
        pstmt.close();
        rs.close();
    }
}
```

위 코드를 통해 우리는 알 수 있는 점은 자바에서 DB로 연결하여 처리하는 경우 JDBC Driver을 로드하고 Connection 객체를 받아와야 한다

즉, 요청이 있을 때마다 JDBC Driver을 로드하고 Connection 객체를 생성하여 연결 및 종료 과정을 거치기에 매우 비효율 적이다

이러한 단점 때문에 Connection Pool을 사용한다

<div align='center'>
    <img src="img/dbcp_4.png" width="550px">
</div>

위 사진과 같이 백엔드 서버와 DB 서버가 존재한다고 가정

백엔드 서버가 API 요청을 받고 DB 데이터 조회 경우 발생

쿼리를 DB로 보내서 데이터를 찾고 쿼리 응답을 한다

처리를 한 다음 API 응답

보통 두 개의 서버는 각각의 컴퓨터에서 수행, 즉 네트워크 통신 과정이다

높은 송수신 신뢰성을 장점으로 한 `TCP` 기반으로 통신한다

TCP는 연결지향적이고 신뢰성을 구축하지만 매번 connection을 열고 닫는 시간적 비용이 발생한다

이를 해결하기 위해 `DBCP(DataBase Connection Pool)`이 등장하였다!

# 데이터베이스 커넥션 풀(DBCP)이란?

<div align='center'>
    <img src="img/dbcp_5.png" width="550px">
</div>

웹 컨테이너(WAS)가 실행되며 일정량의 Connection 객체를 미리 만들어서(TCP 기반) pool처럼 관리한다

클라이언트 요청이 오면 Connection 객체를 빌려와 쿼리 요청을 보낸다

작업이 끝나면 Connection Pool에 Connection 객체를 반환한다(Close Connection)

이러한 Connectoin Pool을 **DBCP(DataBase Connection Pool)**이라 부른다

## 데이터베이스 커넥션 풀(DBCP) 동작 원리

다양한 DBCP 중 하나인 HikariCP를 예로 설명

1. 유휴 Connection 찾기(쓰지 않고 놀고 있는 Connection)

<img src="img/dbcp_6.png" width="550px">

- Thread가 Connection을 요청하면 Connection Pool 방식에 따라 유휴 Connection을 찾아서 반환한다.

- Hikari CP의 경우, 이전에 사용한 Connection이 존재한다면, 우선적으로 반환한다

2. 만약 유휴 Connection이 존재하지 않는 경우 HandOffQueue에서 기다림

<img src="img/dbcp_7.png" width="550px">

- HandOffQueue를 Polling 하여 다른 Thread가 Connetion을 반납하기를 기다린다

3. HandOffQueue에서 유휴 Connection 획득하기

<img src="img/dbcp_8.png" width="550px">

- 최종적으로 사용한 Connection을 반납하면 Connection Pool이 Connection 사용 내역 기록, HandOffQueue에 반납된 Connection 삽입한다

- 이를 통해 HandOffQueue를 Polling하던 Thread는 Connection을 획득하고 작업을 계속한다

## 데이터베이스 커넥션 풀(DBCP) 장점

- DB 접속 설정 객체를 미리 만들어 연결하여 메모리 상에 등록하기에 커넥션 생성 및 삭제 과정이 사라져 클라이언트가 빠르게 DB에 접속이 가능하다

- DB Connection 수를 제한할 수 있어 과도한 접속으로 인한 서버 자원 고갈 방지 가능

- DB 접속 모듈을 공통화하여 DB 서버의 환경이 바뀌어도 쉬운 유지 보수 가능

- 연결이 끝난 Connection을 Connection Pool에서 재사용함으로써 새로 객체를 만드는 비용 줄임

## 데이터베이스 커넥션 풀(DBCP) 설정 방법

DB는 MySQL를 예로 들어 설명, DBCP는 SpringBoot 2.0부터 들어간 HikariCP로 설명

DB connection은 BackendServer와 DB서버 사이의 연결을 의미하기에 설정 방법을 잘 알고 있어야한다

### DB 서버 설정방법

MySql에는 중요한 두 가지 파라미터 존재

1. `max_connections` : client와 맺을 수 있는 최대 connection 수

   - 클라이언트 서버가 증설되어 이 수치보다 더 많은 커넥션의 연결을 요청해도 이 수치 이상으로 커넥션을 할당하지 않음

2. `wait_timeout` : connection이 inactive(놀고있는 상황)할 떄 다시 요청이 오기까지 얼마의 시간을 기다린 뒤에 close 할 것인지를 결정
   - 비정상적으로 connection이 반환되지 않아서 요청이 들어오지 않을 수 있으므로, 만료 시간을 설정하는 것이 필요.

### DBCP 설정

HikariCP 기준

1. `minimumIdle` : pool에서 유지하는 최소한의 Idle connection 수

   - Idle: inactive 상태인 유휴자원 의미
   - 즉, connection이 연결되었지만 아무런 일을 하지 않는 유휴자원의 수를 결정하는 파라미터

2. `maximumPoolSize` : pool이 가질 수 있는 최대 connection 수

   - Idle + active 커넥션 총 개수
   - minimumIdle보다 우선순위가 높음
     - ex) minimumIdle 이 2이고, active 커넥션이 3일 때 Idle connection 이 없기 때문에 2개의 커넥션을 생성해야 하지만 maximumPoolSize 가 4로 제한되어있다면 1개만 더 생성한다.

3. `maxLifetime` : pool에서 connection의 최대 수명
   - maxLifetime을 넘긴 시점에 해당 connection이 idle일 경우 pool에서 바로 제거, active인 경우 pool로 반환된 후 제거

**권장 사항!**

maxlifetime 설정 시 db connection time limit보다 몇 초 짧게 설정해야한다

4. `connectionTimeout` : pool에서 connection을 받기 위한 대기 시간.

   - 즉, backend서버로 요청이 들어왔을 때 요청을 처리하기하다가 dbserver로 접근할 일이 발생하였을 때 connectionPool에서 connection을 받아야하는데 이 때 소요되는 시간을 의미

---

### 📢 면접 질문

1. Connection Pool 이란?
2. Connectoin Pool을 왜 쓰는가?
3. 실시간 통신과 Pool 사용시 차이가 무엇인가?
