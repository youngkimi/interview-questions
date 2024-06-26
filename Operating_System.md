# Operating System

## 1. 프로세스와 스레드의 차이

### 프로세스

- OS로부터 직접 자원(CPU, 메모리)과 주소 공간을 할당받아서 실행되는 프로그램.
- OS가 관리하는 작업 단위라고 할 수 있다.
- 메모리는 `code`, `heap`, `stack`, `data` 영역으로 구성되며, 각각을 segment라고 부른다.
  - 코드(Code) 영역: 실행할 프로그램 코드가 저장되는 영역.
  - 데이터(Data) 영역: 초기화된 전역 변수와 정적 변수가 저장되는 영역.
  - 힙(Heap) 영역: 동적 메모리 할당 시 사용되는 영역.
  - 스택(Stack) 영역: 함수 호출 시 지역 변수, 매개 변수, 리턴 주소 등이 저장되는 영역.
- 각각의 프로세스는 공유하지 않는, 별도의 메모리 영역을 갖는다.

#### 프로세스 제어 블록(Process Control Block, PCB)

- 운영체제가 프로세스에 대한 정보를 저장할 때 사용하는 자료구조.
- 프로세스가 CPU를 할당받아 작업을 하던 중, `context swtich`가 발생해 작업을 중단하게 되면, 작업 진행 상황을 PCB에 저장한다.
- 이후 다시 CPU를 할당받을 때, 이전 작업 정보를 PCB에서 불러온다.

#### PCB 에 저장되는 정보

- 프로세스 식별자(Process ID, PID) : 프로세스 식별번호
- 프로세스 상태 : new, ready, running, waiting, terminated 등의 상태를 저장
- 프로그램 카운터 : 프로세스가 다음에 실행할 명령어의 주소
- CPU 레지스터
- CPU 스케쥴링 정보 : 프로세스의 우선순위, 스케줄 큐에 대한 포인터 등
- 메모리 관리 정보 : 페이지 테이블 또는 세그먼트 테이블 등과 같은 정보를 포함
- 입출력 상태 정보 : 프로세스에 할당된 입출력 장치들과 열린 파일 목록
- 어카운팅 정보 : 사용된 CPU 시간, 시간제한, 계정번호 등

### 스레드

- `프로세스` 내에서 실행되는 작업의 흐름. 프로세스의 실행 단위.
- 하나의 `프로세스`는 최소 하나의 스레드를 갖는다.
- 각 스레드는 고유한 `stack` 영역을 가져 함수 호출, 지역 변수 등을 독립적으로 관리한다.
- 나머지 자원과 영역은 서로 공유한다.

## 2. 멀티스레드

한 프로세스에 `스레드`가 여러 개 존재하고, 여러 스레드가 `프로세스`의 작업을 수행하는 것.

### 장점

- 메모리 공간과 시스템 자원 소모가 줄어든다.
  - 멀티 프로세스의 경우 프로세스마다 별도의 메모리 할당이 필요하다.
- 스레드 간 `Context Switch` 비용이 프로세스 간 `Context Switch` 비용보다 훨씬 저렴하다.
  - 캐시 메모리를 비울 필요가 없기 때문이다.
- 멀티 프로세싱보다 효율적으로 작업을 수행할 수 있다.

### 문제점

- 동시성 문제가 발생할 수 있다.
  - 멀티 프로세스는 별도의 메모리 영역에서 각자 자원을 관리하기 때문에, 자원에 대한 동시성 문제가 발생하지 않는다.
  - 하지만 멀티 스레드 환경에서는 자원을 공유하기 때문에 다른 스레드가 사용 중인 자원에 접근하여 동시성 문제를 야기할 수 있다.
  - 이를 해결하기 위한 동기화 작업이 필요하다.
- 오류 발생으로 하나의 스레드가 종료되면, 전체 스레드가 종료될 수 있다.

### 동기화 작업

- 공유 자원을 읽고 쓰는 작업에 대해 락을 통해 적절한 원자성을 확보해야 한다.
- 공유 자원에 대한 가시성 문제를 고려해야 한다.
- 과도한 락 사용은 작업의 효율성을 떨어뜨리고, 심각한 경우 교착 상태(Deadlock)를 발생시킬 수 있다. 따라서 임계 영역은 가능한 최소화해야 한다.

## 3. 스케줄러

프로세스를 스케줄링하기 위한 Queue에는 세 가지가 존재한다.

- Job Queue : 현재 시스템 내에 있는 모든 프로세스의 집합
- Ready Queue : 현재 메모리 내에 있으면서 CPU 를 잡아서 실행되기를 기다리는 프로세스의 집합
- Device Queue : Device I/O 작업을 대기하고 있는 프로세스의 집합

각각의 Queue 에 프로세스들을 넣고 빼주는 스케줄러에도 크게 세 가지 종류가 존재한다.

### 장기 스케줄러 (Job Scheduling)

`created -> ready를 관리하는 스케줄러.`

커널에 등록할 작업(Job)을 관리하는 스케줄러이다. 메모리는 한정되어 있는데 많은 프로세스들이 한꺼번에 메모리에 올라올 경우, 대용량 메모리(일반적으로 디스크)에 임시로 저장된다. 이 pool 에 저장되어 있는 프로세스 중 어떤 프로세스에 메모리를 할당하여 ready queue 로 보낼지 결정하는 역할을 한다.

- 메모리와 디스크 사이의 스케줄링을 담당.
- 프로세스에 memory(및 각종 리소스)를 할당(admit)
- degree of Multiprogramming 제어 (실행중인 프로세스의 수 제어)
- 프로세스의 상태 : new -> ready(in memory)
- I/O-bounded와 CPU bounded 프로세스를 적절히 섞어야 한다.
- 시분할 시스템에서는 모든 작업을 시스템에 등록한다. (장기 스케줄링이 불필요)
- cf) 메모리에 프로그램이 너무 많이 올라가도, 너무 적게 올라가도 성능이 좋지 않은 것이다. 참고로 time sharing system 에서는 장기 스케줄러가 없다. 그냥 곧바로 메모리에 올라가 ready 상태가 된다.

### 중기 스케줄러

`메모리 할당을 결정하는 스케줄러(Swapper)`

- 현 시스템에서 메모리에 너무 많은 프로그램이 동시에 올라가는 것을 조절하는 스케줄러이다.
- 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄 (swapping)
- 프로세스에게서 memory 를 deallocate
- degree of Multiprogramming 제어
- 프로세스의 상태 : ready -> suspended

> Process state - suspended, Suspended(stopped)

외부적인 이유로 프로세스의 수행이 정지된 상태로 메모리에서 내려간 상태를 의미한다. 프로세스 전부 디스크로 swap out 된다. blocked 상태는 다른 I/O 작업을 기다리는 상태이기 때문에 스스로 ready state 로 돌아갈 수 있지만 이 상태는 외부적인 이유로 suspending 되었기 때문에 스스로 돌아갈 수 없다.

### 단기 스케줄러

`ready -> running을 관리하는 스케줄러`

- CPU 와 메모리 사이의 스케줄링을 담당한다.
- Ready Queue 에 존재하는 프로세스 중 어떤 프로세스를 running 시킬지 결정.
- 프로세스에 CPU를 할당(scheduler dispatch)
- 프로세스의 상태 : ready -> running -> waiting -> ready

## 4. CPU 스케줄러

### CPU 스케줄링이란?

- 멀티프로세싱에서 CPU 사용률을 최대화하기 위한 방법
- 프로세스는 CPU 작업과 I/O 작업을 번갈아 진행한다.
- I/O 작업중에는 CPU를 사용하지 않으므로, 다른 프로세스가 CPU를 사용해야 CPU 사용률을 최대화할 수 있다.
- 즉, 어떤 프로세스에 할당해야 가장 CPU 사용량을 최대화할 수 있을까?

### CPU 스케줄링의 목표

- CPU 사용률(CPU의 바쁜 정도)을 최대화.
- Throughput (처리량, 단위 시간 당 처리한 일의 수) 최대화.
- Turnaround Time (반환 시간, 한 프로세스 submit에서 completion까지) 최소화.
- Wating Time (대기 시간, CPU 얻기 위해 대기 Queue에서 기다리는 시간 합) 최소화.

### 선점형 / 비선점형 스케줄링

#### 비선점형 스케줄링

- 프로세스가 CPU 사용 중이라면, 프로세스 종료나 I/O 작업 이전까지는 CPU를 빼앗기지 않음.
- 처리 시간 계산이 용이함.

1. FCFS (First Come / First Served)

- 큐에 도착한대로 CPU 할당.
- 실행 시간이 짧은 작업은 지나치게 오래 기다릴 수 있음. (`convoy effect`)
- 대기시간과 반환시간 증가, 처리량 감소

2. SJF (Shortest Job First)

- CPU Burst Time(CBT)이 가장 짧다고 판단되는 작업을 먼저 수행
- FCFS 보다 평균 대기 시간이 감소. 짧은 작업에 유리.
- CBT가 긴 프로세스의 기아 문제 발생 가능.
- 어떻게 CBT를 정확하게 추정할 것인가?
- 선점형, 비선점형 모두 가능하다.
  - 작업 실행 중간에 더 짧은 작업이 대기 큐에 추가되는 경우 CPU를 빼앗느냐?
  - 즉, 실행중인 작업의 남은 CBT와 대기 큐의 CBT를 비교.
  - 선점형의 성능이 더 좋기는 하다. SRTF (Shortest Remaing Time First)라고도 한다.

> How to predict CPU Burst Time (CBT)

- 일반적으로 과거 이력 (과거 해당 프로세스의 CBT)을 기준으로 판단한다.
- 과거에 적게 썼다면, 이번에도 적게 쓸 확률이 높다. (경향성 면에서)
- 과거보다는 현재의 경향성에 더 큰 가중치를 주는 것이 맞다.
- 지수 계산(EMA)가 대표적이다.
  - `EMA(n) = a * EMA(n-1) + (1 - a) * P(n-1)`
  - 직전의 정보만을 가지고 있으면 된다. 직전 EMA에 모든 과거 정보가 담겨있기 때문이다.

#### 선점형 스케줄링

- 프로세스가 CPU 사용 중에도, OS가 CPU를 몰수할 수 있음.
- 처리 시간 계산이 어려워짐.

1. Round Robin

- 정해진 시간만큼만 돌아가며 프로세스에 할당.
- 원형 큐로 구현.
- 대기 시간이 늘어난다.
- 정해진 시간을 얼마로 하냐에 따라 성능 차이가 발생한다.
  - 너무 길면 처리량 저하(`FCFS`), 너무 짧으면 오버헤드의 증가 (`context switch`)

> How to determine the optimum time value quantum for RR

-

2. Priority based

- 각 작업에 우선순위를 부여해서 우선순위대로 작업을 수행하는 것.
- SJF도 Priority based로 볼 수 있다. (`CBT`의 역을 우선)
- SJF(SRTF)와 마찬가지로 기아 문제 발생 가능.
  - aging(대기 시간에 따라 우선순위 상향)으로 해결 가능

3. MLQ (Multi-Level Queue) 다단계 큐

- 우선 순위 별로 여러 개의 큐를 설정.
- 큐 별로 다른 알고리즘을 따라 CPU를 할당할 수 있다.
  - 높은 우선 순위의 Queue는 짧은 CBT가 예상되므로 RR.
  - 낮은 우선 순위의 Queue는 긴 CBT가 예상되므로 FCFS.
- 높은 우선순위 큐의 작업이 완료되어야 다음 큐로 넘어갈 수 있다면, 기아 문제가 발생할 수 있다.

4. MLFQ (Multi-Level FeedBack Queue)

- MLQ와 같지만, 작업의 Queue 이동을 허용하는 것
- 높은 우선 순위 Queue의 작업이 한 번의 Time Quantum으로 마무리되지 않으면 다음 Queue로 이동시킨다.
- 높은 우선 순위의 Queue는 반대로 짧은 Time Quantum을, 낮은 우선 순위 Queue는 긴 Time Quantum을 할당한다.
- 처음에는 가장 우선순위가 높은 큐에 작업을 할당한다. 짧은 Time Quantum으로 작업을 수행한다.
- 해당 작업이 마무리 되지 않았다면, 그 다음 큐로 이동한다.
- 이 작업을 반복하면, 짧은 CBT의 작업은 높은 우선순위 큐에서 작업이 마무리 될 것이고, 긴 CBT는 아래쪽 우선순위 큐에서 더 긴 Time Quantum을 할당받아 작업을 마무리할 것이다.
- 거의 실제에 가까운 CPU 스케줄링 알고리즘이다.

> 사실 현재의 CPU는 프로세스 스케줄링을 하지 않는다. 작업의 단위가 프로세스가 아닌 CPU이기 때문이다.

> thread 중에서도 kernel thread만 스케줄링하면 된다. user thread는 직접적으로 OS가 관리하지 않는다. kernel thread와 user thread를 매핑하면 된다.

## 5. 동기와 비동기의 차이

## 6. 프로세스 동기화

동시에 실행되는 프로세스(스레드)들의 공유 자원에 대해 `Data Inconsistency` 문제가 발생할 수 있다.

### Critical Section(임계 영역)

- 각 프로세스(스레드)가 공유자원을 건드리므로, 원자성이 확보되어야 하는 영역이다.
- 원자성이 확보되지 않는다면, 공유자원을 사용하는 도중에 다른 프로세스(스레드)가 끼어들어 원하지 않는 결과를 초래할 수 있다.

### Requirements(해결을 위한 기본조건)

- Mutual Exclusion(상호 배제)

  프로세스 P1 이 Critical Section 에서 실행중이라면, 다른 프로세스들은 그들이 가진 Critical Section 에서 실행될 수 없다.

- Progress(진행)

  Critical Section 에서 실행중인 프로세스가 없고, 별도의 동작이 없는 프로세스들만 Critical Section 진입 후보로서 참여될 수 있다. (데드락 방지)

- Bounded Waiting(한정된 대기)

  P1 가 Critical Section 에 진입 신청 후 부터 받아들여질 때가지, 다른 프로세스들이 Critical Section 에 진입하는 횟수는 제한이 있어야 한다. ()

> DeadLock

자원(CPU)이 놀고 있는데, 아무도 자원을 할당 못받는 상황

> Starvation

프로세스(스레드)가 자원을 할당받지 못하는 상황

### 해결책

#### 1. Mutex Lock

상호 배제를 구현하는 알고리즘.

- Critical Section을 보호.
- `Entry Section`에서 Lock을 획득한 뒤 `Critical Section`에 진입
- `Critical Section`의 작업을 모두 끝낸 뒤 `Exit Section` 진입하며 Lock 반환
- 두 메서드 (`acquire()`, `release()`)와 lock의 가용성을 나타내는 `available` 변수 구현이 필요.
- `Spin Lock`의 방식.
  - `Busy Wait`하는 lock, lock 대기하는 동안 공회전(spin)
  - lock의 획득까지 자원을 계속 소모하는 문제 발생.
  - CPU Core가 여러 개인 `Multi Core`에서 효율적일 수 있음 (어차피 노는 Core니까)
  - `Context Switch` 시간이 길 때 효과적. (계속 대기 중이라서)
- `Blocking`의 방식
  - lock을 획득하지 못하면 대기하러 감.(`wait()`)
  - 이후 임계 영역의 작업을 모두 완료한 프로세스가 대기 중인 프로세스에게 알려줌.(`signal()`)
  - `Spin Lock`과는 다르게, `Context Switch` 시간이 짧은 작업에 효과적.

```c
acquire() {
  while (!available) {
    // busy wait
  }

  available = false;
}

release() {
  available = true;
}
```

#### 2. Semaphores

`Semaphore(신호기).` n개의 프로세스 간 동기화에 사용한다.

- `semaphore S`는 가용 가능한 자원을 나타내는 정수 값이다.
- 가용 가능한 자원이 없는 경우(S <= 0)는 대기한다.
- 자원 가용 시에는 사용하고 (S--) 사용 후에는 반납한다. (S++)
- 아래는 위 처럼 `Busy Wait`하는 코드이다. `Queue`를 활용해서, `wait()`, `signal()`로 사용하여 해결할 수 있다.
- `S`는 `wait()`, `signal()`이라는 두 가지 원자적 연산으로만 접근이 가능하다.
- `Semaphore`는 작업의 순서를 정해줄 때 사용할 수 있다.
-

```c
wait(S) {
  while (S <= 0)
  ; // busy wait
  S--;
}

signal(S) {
  S++;
}
```

#### 3. 모니터

Mutex, Semaphore의 문제 해결. 최근 Java에서 사용하는 해결책. (`wait`, `notify`)

- 발생하기 쉬운 에러를 방지할 수 있도록 추상화하여 프로그래머가 동기화를 덜 신경쓰게 함.
  - 락을 획득하고 해제하지 않는다던가, `signal()`을 하지 않는다던가 ...
- 프로그래머가 Mutex를 보장할 수 있게 정의한 연산들을 포함하는 ADT(Abstract Data Type)
- `Mutex`, `Condition`로 구성.

- `Mutex`:
  - `Critical Section`에서의 Mutex 보장 장치. 진입을 위해서는 Lock을 취득해야 함.
  - Lock을 획득하지 못하면 `wait()` 대기 상태로 빠짐. (`Entry Queue` 적재)
  - Lock 반환 시에는 `Entry Queue` 내 프로세스 하나를 깨움. (`signal()`)
- `Condition`
  - 조건이 추가되기를 기다리는 스레드들이 대기 상태에서 기다리는 곳.
  - `wait()` : 조건 미충족시, 자기 자신을 `Waiting Queue` 적재. Lock을 반환.
  - `signal()` : `Waiting Queue` 내에서 대기 중인 스레드 하나를 깨움.
  - `broadcast()` : `Waiting Queue` 내에서 대기 중인 스레드 전부를 깨움.

```c
// Lock을 획득.
acquire(m);
// 조건을 확인.
while (!p) {
  // 조건 충족이 안되면 Lock을 반환.
  // cv가 관리하는 waiting queue에 자기 자신을 적재.
  // 대기 상태로 전환.
  wait(m, cv);
}
... // remainder section
// 작업 이후 조건을 주변 스레드에 전파.
// cv might be same with cv2
signal(cv2); // broadcast(cv2);
// 작업 이후 lock 반환.
release(m);
```

두 개의 큐 (Queue)

- `Entry Queue` : `Critical Section` 진입을 기다리는 스레드 큐.
- `Waiting Queue` : `Condition Variable` 조건 충족을 기다리는 스레드 큐.

`Signal` 이후 진행 방법

- `SignalAndContinue` : `signal()` 이후 작업을 이어 나감. 조건 충족한 스레드는 `Entry Queue`로 이전.
- `SignalAndWait` : `signal()` 해당 스레드가 `Entry Queue` 진입. 작업 가능한 스레드에게 락을 넘기고 작업.

Java에서의 모니터

- 자바에서는 모든 객체는 내부적으로 모니터를 가진다.
- 모니터의 `Mutex`는 synchronized 키워드를 통해 사용한다.
- 자바의 모니터는 `Condition Variable`을 하나만 가진다.
- `wait()`, `notify()`, `notifyAll()`

`synchronized`

- `method`, 혹은 `block`에 사용할 수 있다.
- `block`에 사용할 시, lock을 전달해줘야 한다.

### Peterson's Algorithm

임계 영역에 진입할 수 있을 때까지 대기(Lock을 획득할 수 있을 때까지). 자신의 차례가 오면 임계 영역에 진입해서 작업을 한 뒤, 작업이 끝나면 다음 순번에게 락을 전달해준다.

- 문제점 : 진입 영역(Entry Section)에서 원자성이 확보되어야 한다.
- 논리적 해결책뿐 아니라, 하드웨어적 해결책도 필요하다.

#### `TestAndSet`

- Lock을 획득할 수 있는지 확인(진입 영역).
- 해당 연산은 원자적임.
- Java에서는 Atomic 변수를 활용할 수 있음.

#### `CompareAndSwap`

- 기존 값, 새로운 값과 더불어 예측 값이 필요함
- 기존 값이 예측 값과 같다면(Compare) 새로운 값으로 Swap
- 다르다면 해당 작업을 재수행함.
- 이걸 활용해서 위 설명한 Atomic 변수의 원자성을 확보해줄 수 있음.

## 12. 메모리 관리 전략

## 13. 메모리 관리 배경

## 14. Paging

## 15. Segmentation

## 16. 가상 메모리

## 17. 배경

## 18. 가상 메모리가 하는 일

## 19. Demand Paging(요구 페이징)

## 20. 페이지 교체 알고리즘

## 21. 캐시의 지역성

## 22. Locality

## 23. Caching line
