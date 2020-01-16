---
title: "System programming - section 13. 쓰레드 동기화 기법1"
date: 2019-10-30 00:15:30
categories: WindowsSystemProgramming
---

# 쓰레드 동기화란 무엇인가?
- 쓰레드 동기화란 멀티 쓰레드 프로그래밍에서 쓰레드간의 동기화에 관련된 문제로, 컴파일 타임에 발생하는 것이 아닌 런타임에 발생하는 것이다.

## 두 가지 관점에서의 쓰레드 동기화

### 실행 순서의 동기화
- 실행 순서에서 지켜야 할 순서가 있다면 이 순서는 지켜져야 한다.
- 예를 들어 쓰레드 A는 쓰레드 B의 실행 결과를 받아서 수행해야 한다면 쓰레드B가 수행 된 후 그 결과 값을 받아서 쓰레드A가 수행 되어야만 한다.
- 이처럼 순서가 반드시 지켜져야 하는 경우를 실행 순서의 동기화라고 한다.

### 메모리 접근 및 리소스 접근의 동기화
- 같은 메모리 영역에 접근할 떄, 이 떄 반드시 한개의 쓰레드만 접근해야 한다는 것이다.
- 여기서는 접근 순서의 동기화는 필요없고, 반드시 한개의 쓰레드만 접근해야하는데 이를 메모리 접근의 동기화이다.
- 참고해야 할 것은, 실행 순서의 동기화도 결국 메모리 접근을 동기화 시키는 것이다. 하지만 여기서는 순서가 존재하는 것이다. 우리는 순서가 중요한 것이 아니라 한개의 쓰레드만 접근 해야 한다는 것을 주의해야한다.

## 쓰레드 동기화에 있어서 두 가지 방법
- 동기화 기법은 동기화 기법을 제공하는 주체에 따라서 크게 두 가지로 나뉜다.
### user mode 동기화
- 동기화기법을 Usermode에서 제공하는 것이다.
- 장점 :이는 동기화를 위해서 kernel mode로 넘어가지 않아도 된다는 장점이 있다. kernel mode로 스위칭시 성능의 저하가 일어나기 떄문이다.
- 단점 : kernel mode만큼 다양한 동기화 기법을 제공하지 못한다는 단점이 존재
- 방법
    - 크리티컬 섹션 기반 동기화
    - 인터락 함수 기반 동기화
### kernel mode 동기화
- 동기화 기법을 kernel영역에서 제공해 준다는 것이다.
- 장점 : 다양한 기능을 제공해 줄 수 있다.
- 단점 : 동기화 기능 제공을 위해서는 user mode <-> kernel mode 로의 context switch가 발생하므로 이는 성능저하를 유발한다.
- 방법 :
    - 메모리 동기화 기법
        - 뮤텍스 기반 동기화
        - 세마포어
        - 이름있는 뮤텍스
    - 실행순서 동기화
        - 이벤트 기반 동기화

# 임계영역 접근 동기화(메모리 영역 접근의 동기화)
- 임계영역의 정의
    - 배타적 접근(한번에 한개의 쓰레드만이 접근 가능)이 요구되는 공유 리소스에 접근하는 **코드영역**
    - 즉 공유 리소스(메모리)를 임계영역이라고 하는 것이 아니라 그 공유 리소스에 접근 하는 코드 영역을 임계영역이라고 한다.
- 임계영역의 동기화 방법
    - user mode 동기화
        - 크리티컬 섹서 기반
        - 인터락 함수 기반
    - kernel mode동기화
        - 뮤텍스 기반
        - 세마포어 기반

# user mode 동기화
## critical section 기반 동기화
- 이 방법은 critical section에 들어가기 위해서는 열쇠를 받아서 들어갈 수 있으며, 열쇠가 없으면 들어갈 수 없다.
- 따라서, critical section을 들어갈 때는 열쇠를 받아서 들어가고, 나와서는 다시 열쇠를 반납한다고 생각하면 된다.
- 전체적인 flow를 보면
    - key 생성
    - key 초기화
    - key 획득
    - 임계영역
    - key 반납
    - key 소멸
## interlock 함수 기반의 동기화
- 인터락 함수는 전역으로 선언된 변수하나에 대한 접근을 동기화 시킬 때, 최적화 되어 있는 방법이다
- 인터락 함수를 사용할 때, 변수의 주소 값을 전달해 주면 된다.
```c++
LONG InterlockedIncrement(LONG volatile* Addend);
LONG InterlockedDecrement(LONG volatile* Addend);
```
- 이를 제외하고도 다양한 interlock함수가 있으므로 MSDN을 참고하면 된다

# 참고! volatile의 의미!
- volatile은 두 가지 의미로 사용된다.
- 1. 최적화 수행 방지
```c++
int function(void)
{
    int a = 10;
    a = 20;
    a = 30;
    cout << a ;
}
int function(void)
{
    int a = 30;
    cout << a;
}
```
- compiler는 위와 같이 최적화를 할 것이다. 하지만 a변수 선언 시 volatile로 했다면 최적화 없이 모든 명령을 수행하게 된다
- 임베디드 시스템에서는 필요함!!

- 2. 메모리에 직접 연산(cache되는 것 방지)
    - cache되는 것을 방지하여, 바로 메모리에 써지도록 한다.

# 커널모드 동기화
- 임계영역을 처리하는 방법을 kernel영역에서 제공!

## 뮤텍스 기반의 동기화
- 뮤텍스 기반의 동기화는 user mode 방식 중 critical section 동기화 기법과 비슷한 방법이라고 생각할 수 있다.
- 하지만 차이는, mutex 기반의 동기화는 kernel영역에서 제공하는 동기화 기법이라는 점이다.
- 동작방법에 대해서 알아본다.
    - 먼저 다시 review해야 될 것은 kernel object의 상태 정보에 대한 것이다.(mutex는 kernel object이다.)
    - kernel object가 가질 수 있는 상태는 signaled 또는 Non-signaled상태이다.
    - signaled상태 : kernel object가 쉬고 있는 상태
    - Non-signaled 상태 : kernel object가 동작하고 있는 상태
- 동작 sequence
    - 1. mutex생성 및 초기화
    - 2. mutex get (WaitForSingleObject : kernel object non-signaled 상태로 변경)
    - 3. mutex release(ReleaseMutex : kernel object signaled 상태로 변경)
    - 4. mutex kernel object 반납(CloseHandle(mutex handle))

## 세마포어 기반의 동기화
- 뮤텍스는 세마포어의 sub-set이라고 생각하면 된다.
- 즉, 뮤텍스는 임계영역에 동시에 접근할 수 있는 것의 개수는 0이다. 즉 1개만 접근 가능 하지만, 세마포어는 임계역역에 여러개의 쓰레드가 접근 할 수 있도록 count기능을 가지고 있다.
- 동작 방법에 대해서 간단하게 설명해 본다.
- count = 10으로 값을 줬다고 하면, WaitForSingleObject함수는 10번 호출 때 까지 blocking되지 않고 쓰레드들이 접근이 가능하다. 11번 째 부터는 blocking된다.
- ㄱ
