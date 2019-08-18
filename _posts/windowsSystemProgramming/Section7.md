---
title: "System programming - section 7. 프로세스간 통신1"
date: 2019-08-11 00:15:30
categories: WindowsSystemProgramming
---
# 프로세스간 통신(IPC)의 의미
- IPC(Inter-Process Communication)는 프로세스들이 서로 데이터를 주고 받는 행위라고 할 수 있다.
- IPC는 메모리 공유라고 이해하는 것이 더 좋다.
    - 즉, 공통으로 사용하는 메모리를 잡고 이를 공유하는 방식이다.
## 프로세스들이 서로 만날 수 없는 이유
- 프로세스는 자신에게 할당 된 메모리 공간 이외에는 접근이 불가능 하다.
    - 자신에게 할당된 메모리 구조가 있기 때문이다.
- 따라서, 두 프로세스가 접선할 수 있는 메모리 공간을 마련하는 것은 불가능 하다.
- 결론적으로 둘이 통신할 수 있는 방법을 생각해봐야 한다.(택배나 소포 같이 정보를 전달해주는 방식을 이용)

## 프로세스들이 서로 만나지 못하게 디자인된 이유
- 오늘날의 운영체제들은 프로세스가 자신에게 할당된 메모리 공간에 이외의 영역에 접근하는 것을 허용 하지 않는다. 이 이유는 무엇일까?
- 메모리 공간이 서로 공유 된다거나 만날 수 있게 허용되 있다고 가정해 보자. Process A가 동작 중인데 Process B가 Process A의 메모리 영역을 변경하게 된다고 하면 Process A의 동작의 안정성은 보장 받지 못할 것이다.
- 따라서 OS에 의해서 Process간에는 서로 만나지 못하게 분리한다.
- 그렇다면 OS가 막았으니, OS가 해결해 줘야 한다.

# 메일슬록 방식의 IPC
## 메일 슬롯 원리
- 메일 슬롯원리는 대표적인 IPC원리이다.
- 이는 매우 간단한 방법이다
- 조건
    - 1. receiver가 우체통을 만든다(by OS)
    - 2. sender process는 receiver의 주소를 알아서, 보내 놓는다.
    - 3. receiver는 이 데이터를 가지고 가면된다.
- 특징
    - 통신의 방향은 단방향이다.
    - 브로드캐스팅 방식의 통신을 지원한다.

# Signaled & Non-Signaled
- 이는 kernal object내에 있는 상태 관련 Field의 상태 값이라고 생각하면 된다.

## Kernel object의 두 가지 상태
- 상태에 대한 이해
    - Kernel object는 두 가지의 상태를 지닌다.
    - **Signaled : 신호를 받은 상태 (process가 종료된 상태), 쉬고 있는 상태**
    - **Non-signaled : 신호를 받지 않은 상태 (process가 실행 중인 상태), 동작 중인 상태**
    - 프로세스가 종료 되었는데 다시 살릴 수 있는가?! 불가능 하다.
    - 따라서 프로세스의 커널 오브젝트(모든 커널 오브젝트가 그런 것 아님!)는 Non-signaled에서 Signaled로 변경 시, 다시 Non-signaled로 변할 수 없다.
    - 정리하면, kernel object의 상태를 통해서 현재 kernel object가 담당하는 대상이 동작 중(Non-signaled)인지 아니면 종료(signaled)되었는지 알 수 있다.
    - 프로세스 커널 오브젝트는 처음 생성 시, Non-signaled 상태에 있다가 종료시 OS에 의해서 signaled로 변경이 된다.

## Kernel object의 두 가지 상태를 확인하는 용도의 함수.
```c++
DWORD WaitForSingleObject(){
    HANDLE hHandle,
    DWORD dwMilliseconds
};
```
- **이 함수를 통해서 process가 signaled가 되기를 기다리는 함수이다.**
- 즉, 부모 프로세스에서 자식 프로세스에게 일을 주고 자식 프로세스의 결과 값을 이용해서 그 다음 과정을 수행해야 한다면, 자식 프로세스가 끝날 때까지 기다려야 하는 것이다. 이때, 사용할 수 있는 함수가 WaitForSingleObject() 함수이다.
