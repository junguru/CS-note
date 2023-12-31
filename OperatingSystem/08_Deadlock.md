# Deadlock
* 대기 중인 스레드가 다른 대기 중인 스레드의 자원을 요청해 무한히 대기하는 문제
    * CPU 사이클, 파일, IO장치 등 유한한 자원을 경쟁
* 락, 세마포어 등 동기화 도구가 가장 주요한 데드락의 원인임
* 스레드의 명령어 간 실행 순서에 따라 데드락이 발생할 수도, 발생하지 않을 수도 있어 예측이 어려움

<br/>

## Livelock
* 둘 이상의 스레드가 전혀 진행되지 않지만, 수행에 **실패하는 동작**을 무한히 반복함
    * 락을 획득하고 해제하는 동작을 무한히 반복하는 등
* 스레드가 대기한 채 멈춰있는 데드락과는 달리 무언가 동작을 **계속 수행**함
* 데드락을 회피하고자 하지만 코드를 불완전하게 작성하는 경우 발생 가능
* 락 획득에 실패한 후 **랜덤 시간 이후**에 재시도하는 방법으로 회피 가능

<br/>

## 데드락의 특징
### 데드락 발생 필요 조건
1. `mutual exclusion`
    * 공유 자원을 여러 스레드가 동시에 접근할 수 없음
2. `hold and wait`
    * 다른 자원을 기다려야 할 때 현재 가지고 있는 자원을 해제하지 않고 가지고 있음
3. `no preemption`
    * 자원은 선점되지 않고 스레드가 작업 완료 후 스스로 해제함
4. `circular wait`
    * 여러 스레드 간 대기하고 있는 관계에 사이클이 발생함
* 네 가지의 조건이 모두 만족될 때 데드락이 발생


### Resource Allocation Graph
* 프로세스와 자원을 정점, 자원 요구 관계를 간선으로 나타내는 그래프
    * `assignment edge` : 자원의 할당을 간선으로 표시
    * `request edge` : 프로세스의 자원 요청을 간선으로 표시
* 그래프를 통해 스레드 관계를 시각화하고 사이클을 통해 데드락 유무를 파악
    * 사이클이 없다면 데드락이 발생하지 않음
    * 인스턴스가 한 개인 자원이 사이클에 포함된다면 데드락 발생
    * 인스턴스가 여러 개인 자원이 사이클에 포함될 경우 데드락이 아닐 수 있음

<br/>

## Deadlock Prevention
* 네 가지 필수 조건 중 하나 이상이 **성립되지 못하게** 해 데드락을 **예방**하는 방법
* CPU와 장치 이용률, throughput이 저하되는 단점

### Mutual Exclusion
* 상호 배제 조건을 성립하지 못하게 하려면, 자원이 동시 접근 가능하도록 허용
* 락 등의 자원은 근본적으로 공유 불가능하므로 상호 배제를 통해 데드락 예방은 **불가능**

### Hold and Wait
1. 스레드 **실행 전** 필요한 모든 자원을 요청하는 방법
    * 자원 요청은 동적으로 발생 가능하므로 비현실적임
2. 자원을 요청하기 전에 현재 **자원을 해제**하는 방법
* 지원 이용률이 저하되고 기아 문제 발생 가능한 문제점

### No Preemption
1. 스레드가 다른 자원을 대기해야 하는 경우, 기존에 가지고 있던 자원을 모두 선점 가능하게 하는 방법
2. 자원 요청 시 다른 대기 중인 스레드에 할당되어 있다면 선점하는 방법
* 자원의 **선점을 허용**해 데드락을 방지
* 락이나 세마포어 등 데드락이 흔하게 발생하는 자원에 대해서는 적용 어려움

### Circular Wait
1. 자원에 대한 순서를 매기고, 스레드가 순서별로 자원을 요청하는 방법
* 자원 할당 그래프에서 **사이클**이 발생하지 않게 함

<br/>

## Deadlock Avoidance
* 자원에 대한 추가적인 정보를 이용해 데드락을 회피
    * 스레드가 요청하는 자원의 순서 등의 정보 활용

### Safe State
* `safe state`
    * 데드락을 회피하면서 스레드에 자원을 할당할 수 있는 순서
    * 당장 가용하지 않더라도 먼저 할당된 스레드 종료 후 할당될 수 있다면 safe state임
* unsafe state인 경우 데드락이 발생할 가능성이 있음
* 자원의 할당은 시스템이 safe state를 유지하는 경우에만 허용됨

### RAG Algorithm
* `claim edge` : 스레드가 미래의 어느 시점에 자원을 요구하는 것을 나타내는 간선
* request 간선을 assignment 간선으로 변환 시 사이클이 형성되지 않을 경우만 자원 할당
* RAG 알고리즘은 단일 인스턴스 자원에 대해서만 적용 가능하다는 한계

### Banker's Algorithm
* 효율성은 떨어지지만, 인스턴스가 여러개인 공유 자원에 대한 데드락 회피 가능
* 자원 별 가용한 인스턴스의 수를 관리하며 safe state 만족하는지 검사
    * `available` : 자원 별 가용한 인스턴스의 수
    * `max` : 각 스레드의 각 자원에 대한 최대 요구량
    * `allocation` : 스레드 별 할당된 자원 인스턴스의 수
    * `need` : 각 스레드가 각 자원을 추가로 필요로 하는 양

<br/>

## Deadlock Detection
* 시스템이 데드락 방지/예방 과정 없이 스레드를 실행하는 경우
* 시스템에 데드락이 발생되었는지 여부를 주기적으로 검사해야 함

### 탐지 알고리즘 호출 주기
1. 자원 할당 요청이 즉시 승인되지 않을 때마다
    * 데드락을 발생시킨 특정 스레드를 식별할 수 있는 장점
    * 자주 실행되기 때문에 오버헤드가 크다는 단점
2. 일정한 시점마다 주기적으로

### Wait-For Graph
* 인스턴스가 하나인 자원에 대해 사이클이 발생하면 데드락이 발생
* 인스턴스가 여러 개인 자원인 경우 추가 자료구조 정의 필요

<br/>

## Deadlock Recovery
* 데드락을 감지 한 뒤 시스템이 데드락을 해소하는 방법
    1. 프로세스를 **abort**
    2. 자원을 **선점**

### Victim Selection
* 어떤 프로세스를 선택해 종료시켜야 복구 비용이 최소인지 결정
    * 프로세스가 점유한 자원의 수
    * 지금까지 실행된 명령어의 양 등

### Rollback
* 프로세스를 안전한 상태까지 되돌린 후 그 상태부터 재시작해야 함
* 안전한 상태가 어디인지 알기는 힘드므로, 처음부터 재시작하는 total rollback
* 롤백을 통해 데드락을 해결하기 위해서는 프로세스에 대한 상태 정보를 유지해야 함

### Starvation
* 비용을 기반으로 롤백시킬 프로세스를 선택하면 같은 프로세스가 계속 실행되지 못할 수 있음
* 선택 기준 비용에 지금까지 롤백된 횟수 등을 고려해 victim 선택