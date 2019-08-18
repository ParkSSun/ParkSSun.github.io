---
title: "System programming - section 6. 커널오브젝트와 오브젝트 핸들"
date: 2019-08-11 00:13:00
categories: WindowsSystemProgramming
---
# 커널 오브젝트에 대한 이해
---
## 커널 오브젝트의 이해
- **커널에서 관리하는 중요한 정보를 담아둔 데이터 블록을 가리켜 커널 오브젝트**라고한다.
- 그럼 Kernel이란?
    - 운영체제(OS)의 핵심기능들을 Kernel이라 한다.
    - 즉, 요즘 Windows나 Linux의 경우 핵심기능 이외에 다양한 Application을 제공해주는데, 이를 제외하고 실제 OS가 기본적으로 해야할 일 공통적으로 가지고 있는 부분을 Kernel이라 한다.
    - 핵심 기능만 두고 보면, **Kernel = 운영체제** 라고 이해해도 된다.
- 예를 들어 보자. 우리가 A 라는 프로그램을 수행시킨다고 할 때 어떤 일들이 발생하는지 정리해 보겠다.
    - Request (User to OS): A 프로그램에 해당하는 프로세스를 만들어 주세요
    - OS가 Process를 생성 할 수 있다면, Process를 생성.
    - 이 때, 이 Process에 대한 정보들을 관리해야 할 자료구조가 필요.
    - windows 운영체제 개발자들이 구조체를 정의함.
        -  이 구조체는 프로세스 상태 정보를 저장하기 위해서 정의한 것.
        -  이 구조체는 프로세스 가 생성 될 때마다, 상태정보를 저장하기 위해서 정의 한 것
        - 이것이 바로 커널 오브젝트이다.
        -  커널 오브젝트에 들어 있을 수 있는 정보로 예를 들면, 프로스세 상태정보(Running, block, ready, end, start)등의 정보들을 가지고 있을 수 있다.
        - **Windows 운영체제는 프로세스, 쓰레드 혹은 파일과 같은 리소스들을 원활하게 관리하기 위해서 필요한 정보를 저장한다. 이 때, 데이터를 저장하는 메모리 블록을 가리켜 커널 오브젝트라 한다.**
## 그 이외의 커널 오브젝트
- Kernel object는 모두 같은가?!
- Ans) 모두 다 다르다. 그 이유는 관리하는 resource가 모두 다른데 같은 구조체를 가진다는 것은 말이 안된다. 따라서 파일을 관리하는 경우 파일 kernel object가 생성되는 것이다. process 의 경우 process kernel object가 생성되는 것이다.

## 오브젝트 핸들을 이용한 커널 오브젝트의 조작
- kernel에 직접접근하는 것이 가능한가?!
- ans) 불가능하다!
- 정책적으로 Kernel에 접근하기 위해서는 kernel object handler에 의해서만 접근이 가능하다.
### 프로세스의 우선 순위 변경
```c++
BOOL SetPriorityClass(
    HANDLE hProcess,
    DWORD dwPriorityClass
);
```
- 위의 코드를 보면
    - hProcess : 우선 순위를 변경할 프로세스의 핸들을 전달
    - dwPriorityClass : 적용할 우선순위 정보를 전달
- 이 떄 전달하는 Handle 정보는 커널오브젝트에 할당된느 숫자일 뿐이다.

## 커널 오브젝트와 핸들의 종속 관계
- 커널 오브젝트는 프로세스에 종속 적인가?
    - Ans) 커널 오브젝트는 운영체제(kernel)에 종속적이다.
    - 즉, process가 없어 진다고 kernel object가 사라지지는 않는다.
    - 예를 들어, 자식 process가 종료하면서 Return값을 1로 반환하고 process를 종료했다고 한다면, 부모 process 및 자식 process에 대해서 관심을 가지고 있던 process들은 자식 process가 어떤 상태로 없어졌는지 확인하고 싶어 한다. 이 때, 자식 process의 kernel object가 사라졌다면, 이런 값들은 확인 할 수 없을 것이다. 하지만, 자식 process의 kernel object는 남아 있으므로 return값을 확인 할 수 있는 것이다.
-커널 오브젝트에 대한 핸들 값은?
    - 각 kernel object의 handle값은 process에 종속적이다. 즉, kernel object handle값이 kernel object의 고유 이름표는 아니라는 이야기 이다.
    - 각 process별로 kernel object handle table을 가지고 있는데, 여기에 각각 kernel object의 handler값이 저장되고 이에 따른 kernel이 연결되어 있다. 따라서 각각 process에서 분별만 가능하면 되므로 각각 process별로 kernel object table이 존재하며 같은 kernel object라 해도 각각 process에 따라 handler 상수 값이 달라질 수 있음을 알아야한다.

## 커널의 공유
- kernel은 공유가 가능하다.
- 즉 A process가 B process를 생성 한다. B process가 우선 순위를 높혀 놓는다. 그러자 A process가 다시 우선순위를 내려 놓는다.
- Kernel이 공유 가능 하므로 위와 같은 일이 가능 한 것이다.

#  커널오브젝트와 Usage count

## CloseHandler 함수에 대한 정확한 이해
- CloseHandler함수가 하는 일은 무엇인가?!
- CloseHandler는 해당 프로세스가 kernel handler를 반환하고, usage count를 1개 감소시키는 일을 수행한다.
- 많은 사람들이 CloseHandler가 process를 종료시키는 일을 한다고 생각하는데, 아니다!
