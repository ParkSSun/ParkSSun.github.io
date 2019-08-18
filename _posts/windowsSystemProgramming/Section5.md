---
title: "System programming - section 5. 프로세스의 생성과 소멸"
date: 2019-07-30 00:13:00
categories: WindowsSystemProgramming
---
# 프로세스의 이해
---
## 프로세스란 무엇인가?
- 기존에 프로세스란 실행중인 프로그램을 프로세스라고 했다면 이제 좀 더 프로세스의 정체에 대해서 자세하게 알 필요가 있다.
- 예를 들어 우리가 어떤 프로그램을 실행시키기 위해서 실행파일을 클릭했다면 이때, 하드 디스크에 저장되어 있던 프로그램 관련 정보들 (메모리 구조 및 binary)가 메인 메모리로 올라갈 것이고 이 것을 프로세스라고 한다.

## 프로세스를 구성하는 요소
- 프로세스를 구성하는 요소는
    - 1. Memory 구조(Heap, Stack, Data, Code영역으로 구성되는)
    - 2. Register Set
- 이렇게 프로세스별로 독립적으로 할당 받는 Resource는 프로세스를 구성하는 요소로 볼 수 있다.
- Process를 구성하는 범주는 Process가 독립적으로 할당받는 리소스이다.
- Process가 여러개이면 Memory구조도 여러개 인가?
    - 당연히 Process마다 Memory 구조가 존재하게 되므로 여러개이다.
- Why register set이 Process의 범주에 들어가는가?
    - 예를 들어 프로세스 A가 수행되고 있다가 프로세스 B가 수행되려고 하면 CPU는 1개이기 때문에, CPU의 Register Set을 프로세스 B에 맞게 변경이 되어야한다. 이 때, 프로세스A의 Register의 값을 저장 후 포로세스 B의 Register 값으로 Context Switching이 발생.
    - 이는 register도 process별로 관리가 되므로, register set이 process의 범주에 들어간다고 볼 수 있다.

## 프로세스의 스케줄링과 상태변화
---
## 프로세스의 스케줄링
- CPU는 한개인데 그럼 어떻게? 여러개의 프로세스를 동시에 실행시킬 수 있는 것인가?
- 방법은 한개의 CPU가 여러개의 프로세스를 번갈아가면서 실행하는 방법 밖에 없고 사용자는 이것을 알아차리지 못하도록 해야한다.
- 그럼 이렇게 하는 것의 주체는 누구인가?!
    - **스케줄러**
    - 스케줄러는 HW가 아닌 SW로 구현되어 있다.
### 스케줄링의 기본 원리
- **스케줄링 : CPU의 할당 순서 및 방법을 결정짓는 일을 스케줄링**
- **스케줄링 알고리즘 : 스케줄링 할 때 사용하는 알고리즘**
- **스케줄러 : 스케줄링 알고리즘을 실제로 프로세스를 관리하는 운영체제요소**

## 프로세스의 상태변화
- 이는 매우 중요한 파트이다.
- 프로세스는 총 5개의 상태를 가진다.
    - 1. Start
    - 2. Ready
    - 3. Running
    - 4. Blocked
    - 5. End
- 그럼 각 state에서 다른 state로 넘어갈 때의 상황에 대해서 설명한다.
- 1. Start to Ready state
    - 이는 Process가 생성 되면, 바로 Running상태로 가는 것이 아니라 Ready상태로 가는 것이다.
    - 우리가 프로그램을 수행시켜 Process가 생성되었다고 해도, 이를 수행할지 말지 여부를 결정하는 것은 OS이므로 Ready상태로 가서 OS 스케줄러의 선택을 기다리게 된다.
- 2. Ready To Running state
    - 스케줄러에 의해서 선택된 프로세스들은 Running state로 이동하여, CPU를 이용하게 된다.
- 3. Running To Ready state
    - 프로세스가 동작하고 있는데, 원래 자신이 소유할 수 있는 시간이 끝나면 스케줄러에 의해서 Ready state로 다시 이동하게되어 스케줄러의 선택을 기다리게 된다.
- 4. Running To Blocked state
    - 만약 프로세스 동작을 하는데, I/O동작(외부와 네트워크 I/O나 파일 입출력 등 다양)이 발생하게 되면, 이 동작이 끝날 때 까지 CPU는 아무일도 하지 못한 채로 기다려야 한다.
    - 이 때는 다른 프로세스가 CPU를 점유하여 사용하는 것이 더 바람직하다고 볼 수 있다.
    - I/O동작이 일어난 프로세스는 Ready상태로 가는 것이 아니라 Blocked상태로 전이하게 된다.
        - 그 이유는, Ready상태로 전이하게 되면 아직 I/O동작이 끝나지 않았는데, 스케줄러에 의해서 Running상태가 될 수 있다.
- 5. Blocked To Ready State
    - I/O동작이나 이런 외부와의 동작이 끝나면 프로세스는 Ready state로 가서 스케줄러의 선택을 기다리게 된다.
    - **Blocked State와 Ready State와의 제일 큰 차이점은 Blocked state는 스케줄러에 의해서 선택 받지 못한다는 것이고, Ready state는 스케줄러에 의해서 선택을 받을 수 있다는 것이다.**

## 컨텍스트 스위칭(Context Switching)
- 멀티프로세스 운영체제가 프로세스의 I/O동작을 고려한다는 측면에서는 CPU를 쉬지않고 일하게 함으로써 성능향상을 가져오는 것으로 보인다.
- 하지만 실제 HW 관점에서 보면, 이는 실행 중인 Process와 관련된 Register 및 상태 값들을 저장하고 다음 실행 될 Process의 register값 및 상태 값을 복원한다는 관점에서 상당히 Overhead가 큰 작업이다.
- 따라서, 이를 고려하는 것도 운영체제가 해야할 일 중에 하나이다.

## 프로세스의 생성
- 프로세스를 생성하는 방법 중 우리가 주로 사용하는 2가지 방법은 다음과 같다.
    - 1. 바탕화면에서 Double Click하여 .exe 프로그램 실행시키는 방법
    - 2. Program상에서 새로운 프로세스를 생성하여 실행시키는 방법
- 위에서 소개한 두 가지 방법은 하는 방법이 다를 뿐이지 속성을 완전하게 동일하다.
    - 바탕이라는 부모 프로세스에서 자식 프로세스를 실행
    - program이라는 부모 프로세스에서 새로운 자식 프로세스를 실행

### CreateProcess함수의 이해
- Windows에서는 프로세스의 생성을 돕기위해서 CreateProcess라는 함수를 제공하고 있다.
- 부모 프로세스 : CreateProcess function을 호출하는 쪽
- 자식 프로세스 : CreateProcess function을 이용하여 생성되는 Process
```c++
BOOL CreateProcess{
    LPCTSTR lpApplicationName,
    LPTSTR lpCommandLine,
    LPSECURITY_ATTRIBUTES lpProcessAttributes,
    LPSECURITY_ATTRIBUTES lpThreadAttributes,
    BOOL bInheritHandles,
    DWORD dwCreationFlags,
    LPVOID lpEnvironment,
    LPCTSTR lpCurrentDirectory,
    LPSTARTUPINFO lpStartipInfo,
    LPPROCESS_INFORMATION lpProcessInformation
};
```
- CreateProcess의 함수 Argument의 조합은 위와 같다.
- 이 중에서 우리가 알아야 할 것은 lpApplicationName, lpCommandLine, lpStartipInfo, lpProcessInformation이다.
- lpApplicationName : 생성할 process의 실행파일 이름을 넣는다. 첫번째 인자로 생성할 프로세스의 이름을 넣게 되면, Process는 현재 부모 프로세스가 존재하는 Directory에서 실행파일을 찾아서 수행시키려 한다.
- lpCommandLine : 생성할 process의 이름 및 생성할 process에 전달할 인자를 넣어 보낸다. 여기에 실행시킬 프로세스의 이름을 넣게 되면 윈도우표준검색경로를 통해서 실행파일을 찾게 된다.

### 프로세스 생성 단계 설명
#### 1.STARTUPINFO 구조체 변수의 생성 및 초기화
#### 2.현재 디렉터리의 설정 (호출할 프로세스가 있는 경로로 변경)
#### 3.CreateProcess함수의 호출

### 표준 검색 경로
- 1. 표준검색경로 : 실행 중인 프로세스의 실행파일이 존재하는 디렉터리
- 2. 표준검색경로 : 실행 중인 프로세스의 현재 디렉터리
- 3. 표준검색경로 : Windows의 시스템 디렉터리
- 4. 표준검색경로 : Windows 디렉터리
- 5. 표준검색경로 : 환경변수 PATH에 의해서 지정되어 있는 디렉터리
