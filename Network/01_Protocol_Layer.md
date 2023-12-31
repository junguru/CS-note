# 인터넷, 프로토콜, 계층화 원칙

## Internet
* 인터넷이란, **네트워크의 네트워크**임
    * 이더넷, 블루투스, 무선랜 등 네트워크 기술을 연결해 줌
* 서로 호환되지 않는 네트워크 간 번역기를 만들면 $O(N^2)$ 쌍의 번역기가 필요
    * 새 통신 기술을 추가할 때마다 N개 번역기를 추가로 만들어야 해 확장성이 좋지 않음
* 대신 **공통의 언어**를 두고 각 기술을 공통 언어로 번역
    * `IP` : Internetworking Protocol이 공통 언어로 사용되기 위해 고안된 통신 프로토콜
    * 매 라우터를 지나며 IP 데이터그램을 꺼내 각 통신 프로토콜에 맞는 패킷 형식을 만들어 전달함

<br/>
<br/>

## Protocol
* 네트워크 통신 시 특정한 **규칙에 따라** 통신이 이루어지도록 정의한 언어
* 프로토콜은 다음 세 가지 조건을 정의해야 함
    1. 메시지 형식
    2. 메시지 교환 순서
    3. 내부 행동

### 1. 메시지 형식
* 자연어의 문법에 대응. 컴퓨터가 0,1로 이루어진 메시지를 보고 어떤 프로토콜인지 모호성 없이 인지


### 2. 메시지 교환 순서
* 통신이 문제 없이 이루어지기 위해 어떤 패킷이 어떤 순서로 교환되어야 하는지 정의

### 3. 내부 행동
* 메시지를 수신 후 수신자 측에서 어떤 행동을 해야 하는지 정의

### 프로토콜의 관리
* **IETF**에서 프로토콜을 작성, 관리하고 **RFC** 문서로 정리함
* **TCP/IP Suite**
    * IP와 더불어 가장 많이 사용되는 프로토콜인 TCP
    * 그리고 이를 기반으로 동작하는 프로토콜의 체계가 TCP/IP Suite

<br/>
<br/>

## 계층화 원칙 (layering principle)
### Analogy
* 아마존을 통해 책을 구매하는 경우를 가정
    * 아마존은 책을 미국 우체국에 맡기고, 우체국은 다시 항공사에 맡겨 배송함
    * 하나의 프로토콜이 모든 일을 수행하는 것은 불가능함!
* 통신 프로토콜도 마찬가지임
    * 같은 계층끼리는 같은 프로토콜을 이용해 데이터를 교환
    * 직접 통신하지는 않고, 아래 계층 프로토콜의 서비스를 이용


### OSI 7 Layer & TCP/IP Layers
* TCP/IP Suite
    * 물리, 데이터 링크, 네트워크, 트랜스포트, 응용 계층으로 구성
* OSI 7 Layer
    * 물리, ..., 세션, 표현, 응용계층으로 세부적으로 나누어짐

### 물리 계층 (Physical Layer)
* **물리 매체**를 통해 통신이 일어남. 0과 1의 데이터를 물리 매체에 **어떻게 표현**할 것인지
* 무선 통신 시 매체는 공기, 광통신의 매체는 광섬유 등

### 데이터 링크 계층
* 링크로 연결된 두 개의 컴퓨터(라우터) 사이를 **연결**해 주는 역할
* `frame` : 데이터 링크 계층의 전송 단위
* 라우터를 지날 때마다 다음 동작을 수행
    * 이전 링크의 프레임에서 IP 데이터그램을 꺼냄
    * 이더넷, 와이파이 등 다음 링크의 형식에 맞는 프레임을 구성해 전송
* 비트를 하나씩 보내지 않고, 프레임으로 묶어 전송해 에러, 흐름, 매체접근 제어 등 수행

#### DLL의 기능
* `error control`
    * 전송 에러가 발생하면 재전송하거나 비트를 수정
* `flow control`
    * 데이터가 손실되지 않도록 수신자의 처리 속도에 맞춰 전송
* `medium access control(MAC)`
    * 여러 컴퓨터 및 라우터가 매체를 공유하며 데이터 전송함
    * 어떤 라우터(컴퓨터)가 어떤 시점에 매체를 사용할 것인지 관리하는 것

### 네트워크 계층
* 서로 다른 링크 계층 간의 호환성 문제를 해결해 **임의의** 컴퓨터 간 **연결성** 만듬
* 즉, 네트워크 기술을 연결하는 망을 연결하고 길을 찾는 역할 수행
* `routing`
    * IP를 통해 서로 다른 기술을 사용하는 링크 간 통신 가능하지만, 어떤 링크를 경로로 타고 갈지 모름
    * 라우팅 프로토콜을 통해 라우터에서 IP주소를 보고 진행할 다음 링크를 결정

### 트랜스포트 계층
* 망 위로 데이터가 **효율적**으로 흘러 다니게 하는 역할
* 배달의 구간
    * 네트워크 계층 : 컴퓨터(호스트)까지 전송
    * 트랜스포트 계층 : **프로세스** 간 데이터 전송

### 응용 계층
* 응용 간 통신 시 지켜야 할 프로토콜 및 인터페이스를 정의
* 응용 간 통신 방법만 표준화, 실제 통신은 트랜스포트 계층에 의존

### 각 계층의 컴퓨터에서의 구현
* 응용 계층 : 응용 개발자가 응용 프로그램 내에 구현해야 함
* 하드웨어 인터페이스
    * 물리 계층과 데이터 링크 계층의 하부 절반 정도를 구현
    * 계산량이 많아 하드웨어를 통해 처리. 커널 소프트웨어로 처리하면 시스템 속도가 느려짐
    * 디바이스 드라이버를 통해 접근
* 커널 영역
    * 커널 영역에 트랜스포트부터 데이터 링크 계층 절반 정도 구현됨

<br/>
<br/>

## 캡슐화, 멀티플렉싱
* `header`
    * 전송 주소, 목적지 주소 등 데이터 전송에 필요한 모든 정보를 포함
* `SDU`는 상위 계층으로부터 내려온 데이터, `PDU`는 헤더를 붙인 후데이터
* `encapsulation`
    * 데이터 전송을 위해서는 아래 계층의 도움을 받아야 함
    * 물리 계층까지 내려가며 각 계층의 헤더를 붙이는 과정이 캡슐화
* `decapsulation`
    * 각 계층의 데이터가 목적지까지 도착한 경우 헤더를 제거하는 과정
    * 데이터 링크 계층 헤더는 링크 하나를 건너면 제거되고 다음 링크의 헤더가 부착됨
    * 네트워크 계층 헤더는 목적지 호스트(컴퓨터)에 도착하면 제거됨
* `multiplexing`
    * 하위 계층이 **다양한 상위 계층** 프로토콜을 수용해 전달할 수 있음
    * 하위 계층 프로토콜의 **헤더**에 어떤 상위 프로토콜을 가지고 있는지 표시

<br/>
<br/>

## 통신 방법 & 프로토콜의 분류
### simplex vs duplex
* `simplex`
    * 단방향 통신은 언제나 한 쪽 방향으로만 정보가 전달됨
* `full-duplex`
    * 전이중 통신은 데이터가 동시에 양쪽 방향으로 흐를수 있음
* `half-duplex`
    * 반이중 통신은 양방향이지만 한 번에 한 쪽 방향으로만 흐를 수 있음

### connection-oriented vs connectionless
* 연결 지향 프로토콜
    * 논리적인 연결을 만든 뒤 데이터 교환을 시작하는 통신 프로토콜
    * 데이터 전송 시 전송 상태 등의 관리가 필요한 경우
    * TCP 등이 연결 지향 프로토콜로, reliable한 데이터 전송 지원
* 비연결 프로토콜
    * 논리적인 연결 없이 데이터를 전송하는 통신 프로토콜
    * reliable하지 않지만, 패킷이 간단, 오버헤드 적음, 멀티캐스트 가능
    * UDP 등이 비연결 프로토콜

### 패킷 교환 vs 회선 교환
* `switching`
    * 모든 통신 노드 간 회선을 만든다면 $O(N^2)$의 회선이 생겨 오버헤드가 큼
    * 대신 제한된 자원을 노드들이 어떻게 나눠 쓰는지를 교환을 통해 정의
* 패킷 교환
    * 데이터를 패킷 단위로 쪼개어 패킷 단위로 전송
* 회선 교환
    * 과거 전화기같은 아날로그 통신 방법
    * 실제로 전화선을 꽂아서 회로를 만들어 통신을 연결