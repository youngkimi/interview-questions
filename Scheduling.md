## CPU 스케줄링

### 스케줄링

- 멀티프로세싱에서 CPU 사용률을 최대화하기 위한 방법
- 프로세스는 CPU 작업과 I/O 작업을 번갈아 진행한다.
- I/O 작업중에는 CPU를 사용하지 않으므로, 다른 프로세스가 CPU를 사용해야 CPU 사용률을 최대화할 수 있다.
- 즉, 어떤 프로세스에 할당해야 가장 CPU 사용량을 최대화할 수 있을까?

#### 목표

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

> How to predict CPU Burst Time(CBT)

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
- 큐 마다 CPU 할당 시간을 다르게 두어 RR.
  - 높은 우선 순위의 Queue는 긴 Time Quantum 할당.
  - 낮은 우선 순위의 Queue는 짧은 Time Quantum 할당.

4. MLFQ (Multi-Level FeedBack Queue)

- MLQ 처럼 여러 개의 우선 순위가 다른 병렬 큐, 각 큐마다 다른 Time Quantum을 할당한다.
- 높은 우선 순위의 Queue는 반대로 짧은 Time Quantum을, 낮은 우선 순위 Queue는 긴 Time Quantum을 할당한다.
- 처음에는 가장 우선순위가 높은 큐에 작업을 할당한다. 짧은 Time Quantum으로 작업을 수행한다.
- 해당 작업이 마무리 되지 않았다면, 그 다음 큐로 이동한다.
- 이 작업을 반복하면, 짧은 CBT의 작업은 높은 우선순위 큐에서 작업이 마무리 될 것이고, 긴 CBT는 아래쪽 우선순위 큐에서 더 긴 Time Quantum을 할당받아 작업을 마무리할 것이다.
- 거의 실제에 가까운 CPU 스케줄링 알고리즘이다.

> 사실 현재의 CPU는 프로세스 스케줄링을 하지 않는다. 작업의 단위가 프로세스가 아닌 CPU이기 때문이다.

> thread 중에서도 kernel thread만 스케줄링하면 된다. user thread는 직접적으로 OS가 관리하지 않는다. kernel thread와 user thread를 매핑하면 된다.

## 프로세스 생애 주기
