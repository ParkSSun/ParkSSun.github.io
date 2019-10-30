---
title: "System programming - section 12. 쓰레드의 생성과 소멸"
date: 2019-10-15 00:15:30
categories: WindowsSystemProgramming
---
# Windows에서의 쓰레드 생성과 소멸

## 쓰레드의 생성
- Windows에서 쓰레드를 생성하기 위해서 사용하는 함수는 CreateThread함수이다.
```c++
HANDLE CreateThread(
    LPSECURITY_ATTRIBUTES lpThreadAttributes,
    SIZE_T dwStackSize,
    LPTHREAD_START_ROUTINE lpStartAddress,
    LPVOID lpParameter,
    DWORD dwCreateFlags,
    LPDWORD lpThreadId
);
```

- LPSECURITY_ATTRIBUTES
    - 이 인자를 통해서 핸들의 상속 여부를 결정한다.
    - NULL시, 핸들은 자식 프로세스 생성 시 상속 대상에서 제외된다.
- dwStackSize
    - stack의 size가 결정된다.
    - 0 전달 시, 1MB의 크기의 stack이 Default로 잡히게 된다.
- lpStartAddress
    - 쓰레드의 main 함수 (시작함수)를 전달하는 인자이다.
    - 인자 타입은 LPTHREAD_START_ROUTINE인데 다음 아래와 같이 정의되어 있다.

```c++
typedef DWORD (WINAPI *PTHREAD_START_ROUTINE)(LPVOID lpThreadParameter)
typedef PTHREAD_START_ROUTINE LPTHREAD_START_ROUTINE
```
- main thread의 return과 각 thread return의 의미에 대해서 알아보자.
    - main thread의 return은 main process의 종료를 나타낸다.
    - 따라서, thread들을 담고 있는 집이 사라지니 그 안에 있는 thread들은 작업을 마치기도 전에 사라지게 된다.
    - thread return문은 실행한 thread의 종료를 나타낸다.
    - thread의 종료에서 가장 좋은 방법은 thread종료 부분에 return을 사용하여 thread를 종료하는 것이다.
- thread 또한 kernel object의 생성을 동반한다. 따라서 CloseHandle함수 호출을 통해서 resource release를 해줘야 한다.
