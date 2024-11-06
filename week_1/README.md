### 1주차 (동작방식에 대해 깊이 있게 설명, 시범기간...)

- Thread, Runnable, Callable, ExecuterService, Async, CompletableFure, ThreadLocal
- Atomic (CAS), Syncronized (lock), voilate, FolkJoinPool, BlockingDeque
- JVM에서 스레드 동작하는 방식
- 컨텍스트 스위칭 비용이란?
- 병렬 프로그램시 알아야할 인프라 리소스

<br>
<hr>
<br>

### 스레드와 프로세스에 대해...

- 자원 적인 측면
  - 프로세스간에는 자원을 모두 독립적으로 사용, 반면, 스레드의 경우에는 자원을 공유하여 사용
  - 프로세스간 작업 변경 진행시, 분리된 자원으로 스위칭 비용이 큼, 반면에 스레드의 스위칭은 동일 메모리이기 때문에 상대적으로 빠름

- 멀티 프로세스와 멀티스레딩 차이
  - 스레드의 경우, 동일 프로세스내에서 병렬 처리 진행
  - 프로세스의 경우, 다중 프로세스로 병렬 처리 진행

<br>
<hr>
<br>

### 병렬 처리와 동시성 처리의 차이

- 자원적인 측면
  - 병렬처리의 경우, core에 각각 스레드가 할당되어 작업을 처리하는 것을 의미
    - 스레드 n개를 병렬처리할려면, core도 n개가 있어야 한다.
  - 동시성 처리의 경우, core 1개에서 빠르게 스레드가 컨텍스트 스위칭 되면서 작업을 진행
    - ![image](https://github.com/user-attachments/assets/7e7e2086-b565-44e4-bc0e-0640dc4db033)

<br>
<hr>
<br>

### 병렬 프로세스에 대해 리소스 관점에서 고민해야할 부분은?

- CPU의 Core수에 따라 병렬처리가 가능한지 아닌지 알 수 있다.
  - core가 1개이면, 동시성 처리만 가능 

<br>
<hr>
<br>

### 컨텍스트 스위칭이란?

- 컨텍스트 스위칭은 CPU의 Core에 올라간 스레드가 짧은 주기를 가지고 계속 바뀌는 것을 의미
  - 빠르게 스레드를 스위칭하면서, 작업의 정보를 PCB 등에 기록
  - ![image](https://github.com/user-attachments/assets/2ba008c5-bccd-4024-8ef9-eccd3dfb450b)
  - 스레드가 스위칭되면서, 스레드별 고유한 메모리 영역인 stack 정보가 계속 바뀌게 된다.
  - ![image](https://github.com/user-attachments/assets/92b513f4-0ce3-4457-a348-c99727ae539c)

<br>
<hr>
<br>

### 컨텍스트 스위칭에 대해 우리가 고려해야 할 부분

- 과도한 컨텍스트 스위칭이 리소스에 어떤 문제를 야기할까?
  - 다수의 스레드가 특정 작업을 진행하는데 있어, 코어를 점유하지 못하고 계속 스위칭된다면, 전반적인 스레드 작업이 밀릴 수 있다.

<br>
<hr>
<br>

### JVM 스레드 동작 관련하여..

- **JVM 스레드**
  - 가상머신 관리: JVM이 스레드를 관리하며, 플랫폼 독립적
  - 추상화된 스케줄링: JVM이 OS 스레드 위에 자체 스케줄링을 추가
  - Garbage Collection 영향: GC와 같은 JVM 내부 메커니즘이 스레드 성능에 영향을 미침
  - OS 스레드
- **운영체제 관리**
  - OS가 직접 스레드 관리 및 스케줄링
  - 커널 레벨에서 작업 분배: 다중 코어와 직접 상호작용하여 자원 할당
  - 시스템 호출 가능: IO 및 시스템 자원 접근 시, 직접적으로 OS 자원을 활용

<br>
<hr>
<br>

### atomic

java의 AtomicInteger는 Cas 알고리즘을 따른다.

![image](https://github.com/DongGeon0908/java-cafe-java-deep-dive-2024/assets/50691225/dc70da37-6852-4316-a939-c4ba6a37e6e4)

Atomic, Adder, Accumulator 등의 기술 존재

![image](https://github.com/DongGeon0908/java-cafe-java-deep-dive-2024/assets/50691225/622cfa93-7ad1-4627-b4b9-9677d2e224bd)

**CAS(Compare-And-Swap) 알고리즘의 주요 특징**
- 비교 및 교체: 메모리 위치의 값을 특정 값과 비교하고, 같을 경우에만 새로운 값으로 교체하는 원자적 연산을 수행
- 낙관적 동시성 제어: 여러 스레드가 동시에 값을 수정하려 할 때, 실패한 스레드는 대기하지 않고 재시도할 수 있어 성능 향상에 기여
- ABA 문제: CAS는 값이 중간에 변경되었다가 다시 원래 값으로 돌아오는 경우를 처리하지 못하는 문제가 있으며, 이를 ABA 문제라고 지칭

**ABA Problem**
- 값의 재사용: 공유된 메모리 값이 A에서 B로 변경된 후 다시 A로 돌아오면, CAS 연산은 값이 변경되지 않았다고 오인 가능
- 데이터 일관성 문제: 중간에 다른 스레드가 값을 변경한 사실을 인지하지 못해, 데이터의 무결성이 손상될 수 있음
- 해결책: 버전 번호를 추가하거나 AtomicStampedReference와 같은 구조를 사용해 값뿐만 아니라 상태 변화를 추적하는 방식으로 문제를 해결 가능


### synchronized

<img width="462" alt="image" src="https://github.com/user-attachments/assets/46be4d51-3a46-409d-9334-fb12727f0ce6">
- 해당 코드에 대한 스레드가 블록킹되어 사용되어짐.

- Java의 synchronized 키워드는 멀티스레드 환경에서 동기화를 통해 여러 스레드가 동시에 공유 자원에 접근하는 것을 제어하는 메커니즘이다. 이 키워드는 내부적으로 **모니터 락(Monitor Lock)**을 사용하여 구현되며, 하나의 스레드만 임계영역에 접근할 수 있도록 보장한다.

**동작 원리**
- 모니터 락: synchronized 블록 또는 메서드에 진입할 때, 해당 객체의 모니터 락을 획득한다. 다른 스레드는 락이 해제될 때까지 대기한다.
- 임계영역 보호: synchronized는 특정 코드 블록이나 메서드 전체를 임계영역으로 설정하여, 한 번에 하나의 스레드만 이 영역을 실행할 수 있도록 한다. 이를 통해 데이터의 일관성을 보장한다.
- 락 해제: synchronized 블록이나 메서드를 벗어나면 자동으로 락이 해제되어 다른 대기 중인 스레드가 접근할 수 있다.
- 알고리즘 기반 설명
  - 모니터 락 기반: synchronized는 각 객체 또는 클래스에 대해 하나의 모니터 락을 사용한다. 인스턴스 메서드는 해당 객체에 대해, 클래스 메서드는 클래스 레벨에서 락을 걸어 동기화를 수행한다.
  - 락 획득 및 해제: 스레드는 synchronized 블록에 진입할 때 모니터 락을 획득하고, 블록이 끝나거나 예외가 발생해도 자동으로 락이 헤재한다.
  - 동기화 방식: synchronized는 크게 4가지 방식으로 사용한다. 인스턴스 메서드, 클래스(static) 메서드, 인스턴스 블록, 클래스(static) 블록 동기화 등.
- synchronized는 구현이 간단하고 직관적이지만, 성능 저하를 유발할 수 있으므로 필요한 최소한의 코드 영역에만 적용하는 것이 중요한다.

### Java Monitor

**Java 모니터의 동작 원리**
- Java에서 **모니터(Monitor)**는 동기화와 **상호 배제(Mutual Exclusion)**를 제공하는 중요한 메커니즘으로, 객체 수준에서 스레드 간의 안전한 자원 접근을 보장
- 모든 Java 객체는 내부적으로 모니터를 가지고 있으며, 이를 통해 스레드가 동시에 공유 자원에 접근하는 것을 제어
- 모니터는 **뮤텍스(Mutex)**와 **조건 변수(Condition Variable)**로 구성되어 있으며, synchronized 키워드를 통해 동작

**주요 동작 과정**
- 락 획득 및 해제:
  - 스레드가 synchronized 블록이나 메서드에 진입하면 해당 객체의 모니터 락을 획득
  - 다른 스레드는 이 락이 해제될 때까지 대기해야 하며, 락을 해제하는 시점은 synchronized 블록을 벗어날 때
- Wait Set과 Entry Set:
  - Entry Set: 락을 기다리는 스레드들이 대기하는 공간. 락이 해제되면 Entry Set에 있는 스레드 중 하나가 선택되어 락을 획득
  - Wait Set: wait() 메서드를 호출한 스레드는 락을 포기하고 Wait Set으로 이동하여 특정 조건이 만족될 때까지 대기. notify()나 notifyAll()이 호출되면 Wait Set에 있는 스레드가 깨어남
- wait(), notify(), notifyAll():
  - wait(): 현재 스레드는 락을 해제하고 Wait Set에서 대기 상태로 들어감
  - notify(): Wait Set에서 대기 중인 스레드 중 하나를 깨움
  - notifyAll(): Wait Set에 있는 모든 스레드를 깨움

**모니터의 구조**
- Java 모니터의 구조는 아래와 같은 방식으로 동작
  - 락(Lock): 모니터의 문 역할을 하며, 한 번에 하나의 스레드만 락을 획득하고 모니터에 진입 가능
  - Entry Set: 락을 기다리는 스레드들이 대기하는 공간.
  - Wait Set: 조건이 충족되지 않아 대기 중인 스레드들이 모이는 공간.

**모니터 동작 그림**
```
+-----------------------+
|       Monitor         |
|  +-----------------+  |
|  |     Lock         |  |
|  +-----------------+  |
|  |                 |  |
|  |    Entry Set    |  |
|  |   (Waiting for  |  |
|  |     Lock)       |  |
|  +-----------------+  |
|  |                 |  |
|  |    Wait Set     |  |
|  | (Waiting for    |  |
|  | notify/notifyAll)| |
|  +-----------------+  |
+-----------------------+
```

Lock: 모니터에 접근하려면 이 락을 먼저 획득해야 함.
Entry Set: 여러 스레드가 락을 얻기 위해 대기할 때 이곳에 머무름.
Wait Set: 특정 조건이 충족되지 않아 wait() 상태로 전환된 스레드들이 대기하는 공간.

### volatile



### Reference

- [blackberry](https://www.qnx.com/developers/docs/7.1/#com.qnx.doc.sat/topic/events_Context_switch_time.html)
- [패스트캠퍼스](https://fastcampus.co.kr/media_branding_cs)
- [codelatte](https://www.codelatte.io/courses/java_programming_basic/KUYNAB4TEI5KNSJV)
- [accessun](https://accessun.github.io/2017/03/12/Optimistic-locking-and-CAS-algorithm/)

