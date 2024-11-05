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


### Reference

- [blackberry](https://www.qnx.com/developers/docs/7.1/#com.qnx.doc.sat/topic/events_Context_switch_time.html)
- [패스트캠퍼스](https://fastcampus.co.kr/media_branding_cs)
- [codelatte](https://www.codelatte.io/courses/java_programming_basic/KUYNAB4TEI5KNSJV)

