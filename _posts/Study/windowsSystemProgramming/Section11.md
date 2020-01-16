---
title: "System programming - section 11. 쓰레드의 이해"
date: 2019-10-13 00:15:30
categories: WindowsSystemProgramming
---

# 쓰레드란 무엇인가?

## 멀티 프로세스 기반 프로그램
- 한 개의 프로그램 내에서, 둘 이상의 실행흐름을 필요로 하는 경우에는 여러개의 프로세스를 생성하는 것이 멀티 프로세스 기반 프로 그램이다.

## 멀티 프로세스 운영체제 기반 프로그램의 문제점과 새로운 제안
- 하나의 프로그램 내에서 여러개의 프로세스가 존재하는 경우, 빈번한 context switching이 발생하게 된다.
- 빈번한 context switching은 성능에 영향을 미치게 되고, 이는 프로그램 성능 저하에 영향을 주게 된다.
- 저장하고 복원하는 context switching의 정보의 수를 줄여주면, 성능저하를 막을 수 있지 않을까 하는 접근을 해보게 된다.
- 즉 프로세스 A와 프로세스 B가 완전하게 다르다고 하면, context switching이 전반적인 정보에 대해서 일어나야 한다.
- 하지만, 프로그램이 비슷하고 정보의 차이가 단지 10%로 밖에 나지 않는다면, 모든 정보에 대해서 context switching이 발생하는 것은 불합리하다.
- **이런 관점에서 쓰레드라는 것이 나오게 된다.**

## 쓰레드!
- 하나의 프로그램내에서 여러개의 실행 흐름을 가지는 경우, 모든 것을 독립적으로 가질 필요는 없다. 이러한 생각 끝에 나온 것이 바로 쓰레드이다.
- 쓰레드는 하나의 프로그램 내에서 둘 이상의 프로그램 흐름을 만들어 내기 위해서 디자인 된 것이다.
- 쓰레드에 대해서 정리해 보자.
    - 쓰레드는 하나의 프로그램 내에서 여러개의 실행 흐름을 두기 위한 모델
    - 쓰레드는 프로세스 처럼 완벽하게 독립적인 구조가 아니라, 쓰레드들 사이에 공유하는 요소들이 있다.
    - 쓰레드는 공유하는 요소가 있는 관계로 컨텍스트 스위칭에 걸리는 시간이 프로세스보다 짧다.

## 메모리 구조 관점에서 본 프로세스와 쓰레드
- 프로세스와 쓰레드의 차이에 대해서 생각해보자.
- 프로세스
    - code영역, Data영역, Heap영역, Stack영역 모두 독립적이고, Context Switching시 이 모든 정보가 Switching된다.
- 쓰레드
    - code 영역, data영역, Heap영역은 공유 되고, Stack영역만 독립적으로 분리
    - 즉 Heap영역은 공유 되므로, Heap을 통해서 쓰레드간에 서로 통신이 가능하다.
    - Stack영역만 분리 되는 것은, 각자 실행 흐름이 존재하려면 각자 Function도 다를 것이고 하기 때문에 Stack은 필수이다.
## Windows에서의 프로세스와 쓰레드
- Windows입장에서 프로세스는 쓰레드가 동작할 수 있도록 지원 및 쓰레드를 담는 그릇에 지나지 않는다.
- 스케줄러의 단위도 쓰레드 단위로 manage된다.


# 쓰레드 구현 모델에 따른 구분
- 쓰레드가 어떻게 만들어지는 가에 대한 것이다.

## Kernel level 쓰레드와 User Level 쓰레드
- Kernel level thread
    - 프로그래머의 요청에 따라서, 쓰레드를 생성 및 스케줄링하는 주체가 커널인 경우 이를 가르켜서 Kernel level thread라고 한다.
    - 즉, kernel level에서 Thread가 지원된다는 것이다.
    - kernel 영역과 User 영역에 대해서 알아보자
        - kernel 영역
            - 운영체제가 실행 되기 위해서는 운영체제 역시 메모리에 올라가야 하고, 일반 프로그램과 동일하게 실행되는 과정에서 변수 선언 및 동적할당을 통한 메모리를 사용하기 도 한다.
            - 따라서, 이러한 운영체제와 같은 하나의 소프트 웨어를 실행시키기 위해서 필요한 메모리 공간을 Kernel영역 이라고 한다.
        - User 영역
            - 어떤 프로그램이 실행 될 때, 사용되는 메모리 공간(코드영역, 스택 힙 영역, 데이터 영역)을 user 영역이라고 한다.
        - 정리하면, 운영체제가 동작하는 메모리 공간을 kernel영역, user에 의해서 프로그램이 실행되어 사용되는 메모리공간을 user영역이라고 한다.
    - Why? user영역과 kernel영역을 분리?
        - 일반 프로그램이 수행되는 메모리 영역과, 운영체제가 동작되는 메모리 영역을 분리해 놓지 않으면 관리하는 입장에서 어려울 수 있어 User 영역과 Kernel영역을 분리해 놓는다.
    - kenel level thread 유형은
        - 쓰레드에게 일을 시키기 위한 코드는 user가 program으로 만드므로, user level에 존재.
        - scheduler와 쓰레드 정보는 kernel영역에 존재.
        - 즉, 프로그래머는 os에게 Thread를 생성해달라고 요청하고 이를 활용한다.
        - OS(kenel영역에 존재)는 kernel level에서 요청이 들어온 thread를 생성하고 이를 scheduling하게 된다.
- user level thread
    - kernel level에서 쓰레드를 지원하지 않을 때, 생각해볼 수 있는 방법이 user level thread이다.
    - 즉, thread 기능을 제공하는 주체가 user level의 user program이라는 것이다.
    - OS 입장에서는 관리의 단위를 thread로 보지 않고, 프로세스로 본다.
- kernel level thread와 user level thread 중 누가 더 빠른가?
    - kernel level thread는 동작이 일어나기위해서 user mode -> kernel mode 로의 context switching이 필요하다.
    - user level thread는 user level에서 계속 동작이 일어나므로, context switching이 필요없으므로 빠르다.

## kernel mode와 User mode
- 여기서 용어를 한번 다시 정리하고 간다.
    - kernel 영역
        - OS(운영체제)가 동작하는 메모리 영역을 kernel영역이라고 한다.
    - user 영역
        - user의 program이 동작하는 영역을 user영역이라고 한다.
    - kernel level thread
        - OS에 의해서 thread 및 스케줄링이 제공 되고, 이는 kernel 영역에 있으므로 kernel level thread라고 한다.
    - user level thread
        - user에 의해서 thread 기능이 제공(또는 라이브러리로 받아서 linking하여 사용)되므로, 이는 user 영역에서 thread기능이 제공되므로 user level thread라고 한다.
- 보통 프로그램들은 user영역에서 동작하므로, kernel영역에 대한 메모리 access가 일어나지 않으나, C언어와 같이 직접 메모리 access가 쉽게 가능한 경우 kernel영역에 대한 접근이 일어날 수 있다.
- 이를 방지하기 위해서 kernel mode, user mode로 나뉘게 된다.
- user mode에서는 user program이 수행되게 되며, kernel영역에 접근시 violation을 알려준다.
- kernel mode에서는 os가 수행되며 메모리 전영역에 대해서 접근이 가능하게 된다.
- 만약, program을 작성시 운영체제에서 제공하는 시스템 함수를 사용하게 된다면, user mode에서 kernel mode로 변경 후 동작하게 된다.
- **이런 user mode <-> kernel mode 의 context switching은 시스템에 부담을 주는 일이다.**
- Question! user mode 와 kernel mode를 제공하는 주체는 OS인가요?
    - 아니요! processor내에 메모리 보호 기능이 있어 프로세서가 kernel mode와 user mode를 제공한다.

## kernel level thread와 user level thread의 장단점
- kernel level thread
    - 장점 : os에서 thread의 생성 및 관리를 해주기 떄문에 안정성 및 다양한 기능이 제공된다.
    - 단점: context switching으로 인한 문제가 발생한다.
- user level thread
    - 장점 : context switching이 없으므로 빠르다.
    - 단점 : OS는 프로세스 단위로 관리하기 때문에, 한 프로세스 내에 thread가 여러개 존재시, 한개의 thread가 blocking상황이 되면 다른 thread도 함께 blocking이 되는 상황이 발생하게 된다.
