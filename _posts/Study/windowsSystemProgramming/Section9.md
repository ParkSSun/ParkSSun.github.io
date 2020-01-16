---
title: "System programming - section 9. 스케줄링 알고리즘과 우선순위"
date: 2019-10-05 00:15:30
categories: WindowsSystemProgramming
---

# OS의 구분 방법
- 스케줄러의 응답성에 따라 : 일반 OS vs RTOS (Real Time Operating System)
    - 일반 OS :
        - 어떤 프로세스 일을 하고 있는데, 새로운 프로세스 일이 주어진다면 현재 처리하고 있는 프로세스의 일을 어느 정도 완료 후 다음 프로세스로 넘어가게 된다.
        - 따라서 응답성이 느리다.
        - 범용적으로 설계되어 있다.
    - RTOS(Real Time Operating System)
        - 어떤 프로세스 일을 하고 있는데, 새로운 프로세스의 일이 생기면 거의 바로 새로운 일을 시작하게 만든다.
        - 따라서, 사용자 입장에서는 응답성이 빠르다고 느끼게 된다.
        - 그러나, 범용적으로 설계 되어 있지 않고 특정 목적을 위해서 설계되어 있다.
        - 사용하는 영역은 핸드폰 OS, 멀티미디어 장치 OS등 특정 목적을 위해서 설계되어 있는 경우가 많다.
        - RTOS는 하던 일을 끝내고 다음 일로 넘어가는 **데드라인(DeadLine)** 개념의 유무에 따라서 Soft RTOS / Hard RTOS로 구분이 된다.
            - Soft RTOS
                - 일반 OS에 비해서 응답성은 좋지만 데드라인 개념이 약하다.
            - Hard RTOS
                - 정말 0.00001초의 응답성을 요구하여 시작요청이 들어오면 바로 동작해야 하는 그렇지 않으면 생명의 위협을 줄 수있는 시스템을 운영할 때, 사용하는 것이 RTOS이다. 이는 CPU의 성능이 좋다고만 해서 쉽게 설계 가능한 문제가 아니다. 따라서 DeadLine개념이 명확한 OS는 RTOS중 Hard RTOS이다.
    - 따라서 RTOS가 더 빠르고 좋다는 옳지 않은 표현이다. RTOS 빠르게 시작은 시킬 수 있으나 일을 빠르게 끝내는 것은 CPU dependency를 가지는 일이기 때문이다.
    - 범용적인 OS와 RTOS를 성능으로 비교하는 것보다는 필요 상황에 따라서 맞게 쓰는 것이 옳다고 할 수 있다.
- 스케줄링을 하는 방식에 따라 : 선점형 OS(Preemptive OS) vs 비선점형 OS (Non-preemptive OS)
    - preemptive OS
        - 선점형 OS는 지금 A Process(우선 순위 1) 처리 중인데, B process(우선 순위3)이 들어 오면 A process는 하고 밀려나고 B process가 cpu를 차지하게 된다.
        - 즉, 우선순위가 높은 놈이 들어오면 이전 것은 바로 교체가 된다.
        - RTOS 및 요즘 사용되는 일반 OS(windows, Linux)의 경우에도 preemptive OS에 속한다.
    - Non-preemptive OS
        - 비선점형 OS는 지금 A Process(우선 순위 1) 처리 중인데, B process(우선 순위3)이 들어 오더라도 A process는 하던 일을 어느정도 마치고 B process가 cpu를 차지하게 된다.

## 우선순위(Priority) 스케줄링 알고리즘
- 우선 순위 스케줄링 알고리즘은 우선 순위가 높은 process가 다 끝나야, 우선순위가 낮은 process가 수행된다는 것이다.
- 우선 순위가 낮은 process는 Starvation(기아)에 빠지게 된다. 하지만 우선 순위가 높은 process가 I/O동작을 수행하게 되면, 우선 순위가 낮은 process도 수행할 수 있는 시간을 얻게 된다.

## 라운드 로빈(Round Robin)스케줄링 알고리즘
- 우선 순위가 차이나는 경우에는 우선순위 스케줄링 알고리즘을 사용한다고 하였는데, 우선순위가 같은 경우에는 어떻게 되는 것인가?
- 우선 순위가 같은 경우에는 Round robin 스케줄링 알고리즘을 적용한다.
- Round robin 스케줄링 알고리즘은, time slice또는 Quantum이라고 하는 단위를 각각 process들에게 주어지게 하고, 이를 기준으로 CPU 점유 시간을 결정한다.
- 즉 동일 우선순위의 Process가 2개 있다면 한 Process가 time slice또는 Quantum만큼의 시간동안 CPU를 점유하고 작업하였다면 다음 Process에게 CPU사용권한을 넘겨줘야 한다.

## 스케줄링 알고리즘에 의해서 스케줄링이 진행되는 시점
- 철학은 **최대한 스케줄러를 적게 깨우려는 노력을 하는 것**
- 1. Round robin에서 보면 매 타임 슬라이스 마다 스케줄러 동작
- 2. 우선 순위 알고리즘에서 보면 프로세스가 생성 및 소멸 될 때 동작
- 3. 현재 프로세스가 Blocked상태 일 때, 동작

## Priority Inversion
- 예를 들어 프로세스A > 프로세스 B > 프로세스 C 이렇게 있다고 가정해보자.
- 프로세스A가 동작하다가 프로세스 C가 처리해서 넘겨야 할 데이터가 있어서 Blocked상태에 빠진 경우, Priority 스케줄링 알고리즘에 의해서 프로세스B가 수행되게 된다.
- 따라서, 프로세스C가 수행이 될 수 없다.
- 이와 같은 경우를 해결하기 위해서 Priority Inversion이 필요하다.
- 프로세스 A는 Blocked상태에 빠지기 전에 프로세스C가 수행 될 수 있도록, 우선순위 위임을 하고 넘어가면 프로세스A가 Blocked후 프로세스C가 수행될 수 있다.
