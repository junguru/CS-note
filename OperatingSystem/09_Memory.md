# Memory Management

<br/>

## Background
### Basic Hardwares
#### Register
* CPU가 1개의 사이클 클럭으로 직접 접근 가능

#### Main Memory
* 프로그램이 적재되어 작업을 수행하는 공간
    * 기계 명령어는 디스크 주소가 아닌 메모리 주소를 인수로 가짐
    * 실행되는 명령어와 데이터는 CPU가 직접 접근 가능한 저장 장치에 있어야 함
* CPU가 메모리 버스를 통해 직접 접근 가능
* 메모리 접근에 많은 CPU 사이클이 소요됨
    * 캐시 메모리에 최근에 접근한 메모리 요소를 저장해 메모리 접근 시간 감소

#### 프로세스의 주소 공간
* 각 프로세스가 별개의 **독립 메모리 공간**을 사용해야 함
* `base register` : 프로세스의 주소 공간의 시작 위치
* `limit register` : 프로세스의 주소 공간의 크기
* 커널에서만 두 레지스터의 값을 변경할 수 있도록 허용
    * 악의적인 사용자가 메모리를 고의로 변경하는 것을 방지

### Address Binding
* 프로그램이 주소를 나타내는 방식은 소스파일, 목적파일, 실행파일에서 다르게 나타남
* 컴파일, 링크, 로드 과정을 통해 주소 공간을 변환
* 최종적으로 프로세스의 메모리 주소를 고정하는 Binding Time은 

#### Compile Time
* 컴파일 시점에 프로세스가 메모리에 위치하는 주소를 아는 경우
* 시작 위치에서 시작해 변수와 명령어의 메모리 주소를 바인딩
* 프로세스의 시작 위치가 변경되는 경우 다시 컴파일해야 함

#### Load Time
* 컴파일 타임에 프로세스 메모리 위치를 알 수 없는 경우
    * 컴파일 결과 목적 코드는 상대적인 주소를 갖는 `relocatable` 코드
    * 링킹 결과 생성되는 목적 코드는 절대 메모리 주소를 가지는 executable 코드
* 로드 타임에 최종 바인딩

#### Execution Time
* 실행 중 메모리 세그먼트 위치가 바뀌는 경우, 바인딩이 런타임으로 늦춰짐
* MMU 등 하드웨어의 지원이 필요
* 대부분의 OS가 런타임에 최종 주소 바인딩

### Logical vs Physical Address Space
* `physical` : base와 limit 레지스터로 표현되는 실제 메모리 주소
* `logical` : CPU 입장에서 프로세스가 사용하는 주소 공간
* 런타임에 바인딩이 수행되는 경우, 물리 주소와 논리 주소가 다름
* 메모리 매핑을 수행하는 하드웨어를 통해 논리 주소를 물리 주소로 변환

### Dynamic Loading
* 정적 로딩 시실행에 필요한 모든 라이브러리를 실행 파일로 합침
* 프로세스의 크기가 커져 메모리의 활용률이 떨어짐
* 동적 로딩 과정
    1. 루틴들은 실제 호출시까지 적재되지 않고 디스크에 존재
    2. 루틴을 호출 시 메모리에 있는지 확인하고 없다면 적재. 즉 필요할 때만 적재

### Dynamic Linking & Shared Libraries
* 링크 과정이 런타임까지 미뤄짐
* 공유 라이브러리 인스턴스를 하나만 메모리에 적재해 메모리를 절약

<br/>
<br/>

## Contiguous Memory Allocation
* 가상 메모리를 사용하지 않던 초기 메모리 관리 방법
* 프로세스가 이전 프로세스 메모리 영역과 연속한 **인접 영역에 적재**됨

### Memory Protection
* `limit register` : 프로세스의 주소 공간의 크기
* `relocation register` : 프로세스의 주소 공간의 시작 위치
* 다른 프로세스와 레지스터 값을 비교해 올바른 주소 공간이 할당됨을 보장함

### Memory Allocation
* `hole` 이용한 방법
* 프로세스를 가변 크기 파티션에 할당. 파티션은 하나의 프로세스를 포함
* 가용 메모리가 없다면 거절하거나 준비 큐에 프로세스 배치
    

### Fragmentation
* 프로세스의 적재와 해제를 반복하며 작은 메모리 공간이 여러 위치에 생성됨
* 충분한 메모리 공간이 있지만 메모리 조각이 흩어져 프로세스 할당하지 못하는 문제
* 메모리 단편화를 해결하는 방법
    1. 작은 메모리 조각을 큰 메모리 구역으로 합침. 다만 프로그램 및 데이터 옮기는 비용이 큼
    2. 프로세스가 비연속된 주소 공간에 적재되는 것을 허용