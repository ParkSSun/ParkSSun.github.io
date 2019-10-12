---
title: "System programming - section 8. 프로세스간 통신2"
date: 2019-08-18 00:15:30
categories: WindowsSystemProgramming
---
# 핸들 테이블과 오브젝트 핸들의 상속
---
## 프로세스의 커널 오브젝트 핸들 테이블
- 시스템 리소스의 생성 과정에서 커널 오브젝트가 생기고 이를 프로세스 핸들 테이블에 등록 후 핸들을 반환한다.
- 이때, 반환 되는 핸들 값을 가지고 우리는 해당 커널 오브젝트에 접근할 수 있다.
- 그렇다면 우리가 핸들 값을 가지고 접근 한다는 것을 어떤 의미인가?!
    - 우선 Handle 값은 Process 종속적이다. 즉 같은 커널 오브젝트를 가리키더라도, 다른 프로세스는 다른 핸들 테이블 값을 가질 수 있다.
    - 그렇다면, handle값을 가지고 어떻게 오브젝트 커널이라는 실체에 접근 할 수 있는 것인가?!
    - **핵심은 Translation Table이 존재한다고 생각하면 된다**
    - 즉 Exception Vector table과 같이 그 handle값에 가면 커널 오브젝트에 접근 할 수 있는 주소값이 있다라고 생각하면 된다.

| 핸들 | 커널 오브젝트 주소 |
| --- | --- |
| 256 | 0x20000 |
| 128 | 0x30000 |
- **위와 같은 테이블을 핸들 테이블이라 한다.**

### 핸들 테이블의 도입
- 핸들 테이블은 핸들 정보를 저장하고 있는 테이블로서 **프로세스별로 독립적이다.**
    - 프로세스 마다 각자의 핸들 테이블을 가지고 있으며 이를 관리한다.

## 핸들의 상속
- 부모 프로세스에서 CreateProcess 함수를 호출 시, 자식 프로세스가 생성이 되는데 이때, CreateProcess 함수에 전달되는 인자가 무엇이냐에 따라서 부모 프로세스 핸들 테이블에 등록되어 있는 핸들 정보는 새롭게 생성되는 자식 프로세스에게 상속 될 수 있다.
```c++
BOOL CreateProcess{
    LPCTSTR lpApplicationName,
    LPTSTR lpCommandLine,
    LPSECURITY_ATTRIBUTES lpProcessAttributes,
    LPSECURITY_ATTRIBUTES lpThreadAttributes,
    BOOL bInheritHandles, // 상속을 결정하는 인자.
    DWORD dwCreationFlags,
    LPVOID lpEnvironment,
    LPCTSTR lpCurrentDirectory,
    LPSTARTUPINFO lpStartipInfo,
    LPPROCESS_INFORMATION lpProcessInformation
};
```
- bInheritHandles의 값을 통해서 부모 프로세스의 핸들 테이블을 상속 받을지 말지 결정하는 인자이다.
    - TRUE : 부모프로세스의 핸들 테이블 정보는 자식 프로세스로 상속
    - FALSE : 상속이 이뤄지지 않는다.
- Question! **그럼 모든 부모의 모든 핸들 테이블은 상속이 되는 것인가요!?**
- Answer) 그렇지 않다. 핸들테이블에 보면 상속여부를 결정하는 Column이 있는데 여기에 상속여부가 Yes로 되어 있는 것들만 실제로 상속이 이뤄진다.
- 부모 Handle table

| 핸들 | 커널 오브젝트 주소 | 상속여부 |
| --- | --- | --- |
| 256 | 0x20000 | Yes |
| 128 | 0x30000 | No |
| 129 | 0x20000 | Yes |

- 상속 받은 자식 Handle table

| 핸들 | 커널 오브젝트 주소 | 상속여부 |
| --- | --- | --- |
| 256 | 0x20000 | Yes |
| 129 | 0x20000 | Yes |

- 자식에서 또 다른 자식 프로세스가 생길 때, InheritHandles를 True로 하면 또, 상속이 가능하다.

### 핸들의 상속과 커널 오브젝트의 Usage Count
- 상속이 되는 경우에는 Usage count는 어떻게 변하는지 알아보자.
- 상속이 발생하는 경우에는 상속 된 프로세스 핸들에 해당하는 커널오브젝트의 Usage count는 +1 씩 증가하게 된다.

### 핸들의 상속 여부 결정
- 핸들의 상속여부는 리소스가 생성 되는 시점에 결정된다.
- 프로세스를 생성하는 시점에 프로그래머에 의해서 상속의 여부를 결정하는 파라미터를 넣게 되어 있다.
- 위의 CreateProcess함수를 보면 입력 인자 값으로 **LPSECURITY_ATTRIBUTES lpProcessAttributes** 가 있다.
- 포인터로써 이를 우리가 값을 넣어 줌으로써 Handle의 상속여부를 결정 지을 수 있다.
```c++
typedef struct _SECURITY_ATTRIBUTES{
    DWORD nLength;
    LPVOID lpSecurityDescriptor;
    BOOL bInheritHandle;
} SECURITY_ATTRIBUTES,*PSECURITY_ATTRIBUTES;


SECURITY_ATTRIBUTES sa;
sa.nLength = sizeof(sa);
sa.lpSecurityDescriptor = NULL;
sa.bInheritHandle = TRUE;

CreateProcess(..... &sa);
```

- 위와 같이 handle의 값을 TRUE로 넣어줌으로써 상속을 가능하게 하는 것이다.**(핸들 테이블에 상속여부 YES로 만들어짐)**
- 참고로 CreateProcess는 상속 관련 두 가지의 일을 결정해야 한다.
    - 1. 부모 프로세스로부터 핸들 테이블을 상속 받을 것인지 결정(InheritHandle)
    - 2. 지금 생성하는 자식 프로세스를 핸들 테이블에 등록 시, 상속이 가능하도록 할 것인지 결정

## Pseudo 핸들과 핸들의 중복 (Duplicate)
- 현재 실행 중인 자신의 핸들을 얻는 방법 : GetCurrentProcess()함수를 사용하여 얻을 수 있다.
- 이때, GetCurrentProcess()함수의 Return값은 -1이 return되는 데 이는 실제 핸들테이블에 등록 되어 있지 않은 가짜 핸들이고 다만 실행 중인 프로세스를 참조하기 위한 용도로 정의해 놓은 약속된 상수가 반환 되는 것이다.
- 따라서,**이때 반환되는 -1값을 현재 실행 중인 process의 pseudo handle이라고 한다.**
- 그렇다면 진짜 핸들을 얻는 방법에 대해서 알아보자.
    - DuplicateHandle() 함수를 사용하면 된다.
```c++
bool DuplicateHandle(
    HANDLE hSourceProcessHandle,
    HANDLE hSourceHandle,
    HANDLE hTargetProcessHandle,
    LPHANDLE lpTargetHandle,
    DWORD dwDesiredAccess,
    BOOL bInheritHandle,
    DWORD dwOptions
);
```

- Input arguments의 값을 한 번 알아보도록 한다.
    - hSourceProcessHandle : 복제할 핸들을 소유하는 프로세스를 지정한다.
    - hSourceHandle : 복제할 핸들을 지정한다.
    - hTargetProcessHandle : 복제된 핸들을 소유할 프로세스를 지정한다.
    - lpTargetHandle : 복제된 핸들값을 저장할 변수의 주소를 지정한다.
    - dwDesiredAccess : 복제된 핸들의 접근 권한을 지정한다.
    - bInheritHandle : 복제된 핸들의 상속여부를 지정한다.
    - dwOptions : DUPLICATE_SAME_ACCESS를 전달 시, 원본 핸들과 동일한 접근권한을 가지게 된다.
- ProcessA (부모 process)가 ProcessB(자식 process)에게 process A에 있는 256 Handle 정보를 넘기고 싶다면 어떻게 해야 할까?
    - 다음 아래와 같다.
```c++
DuplicateHandle(
    프로세스 A의 Handle,
    256,
    프로세스 B의 Handle,
    &val,
);
```
- 이렇게 하면 프로세스 A의 핸들 테이블에 있는 256번 핸들의 주소 값이 자식 process에 등록되어 진다.
- 주의 할 점은 복사 된다고 해서 핸들 값까지 똑같은 것은 아니다. 핸들 값에 해당하는 주소값이 복사되고, 핸들 값은 다른 값으 가진다는 것을 기억해야 한다.
```c++
DuplicateHandle(
    프로세스 A의 Handle,
    256,
    프로세스 A의 Handle,
    &val,
);
```
- 이렇게 하면 process A의 커널 핸들 테이블에 256번에 있던 kernel handle의 메모리 값이 process A의 핸들 테이블에 추가로 등록 되어 진다.
- 이 때, 해당 커널 오브젝트의 usage count 는 1이 증가한다.

## 부모 프로세스의 핸들을 자식 프로세스에게 전달하기
- 부모 프로세스의 핸들을 스스로 Duplicate하여 Handle table에 등록한 후, 이를 상속 받을 수 있도록 설정한 후 자식 프로세스에게 테이블을 상속하여 주는 것이다.

# 파이프 방식의 IPC
---
## 메일슬롯에 대한 회고와 파이프의 이해
| IPC 종류  | 메일 슬롯 | 이름있는 파이프 | 이름 없는 파이프 |
| --- | --- | --- | --- |
| 방향성 | 단방향, 브로드 캐스팅 | 양방향 | 단방향 |
| 통신 범위 | 제약(x) | 제한(x) | 부모자식 Process |

- 이름 없는 파이프 (Anonymous Pipe)
    - 지극히 관계가 있는 프로세스들 사이에서 통신하는 경우에는 유용하다.
- 이름 있는 파이프 (Named Pipe)
    - 이름이 있다는 것은 주소 정보가 있다는 것이다. 따라서 그 주소를 공유함으로써 데이터를 주고 받을 수 있다는 것을 의미한다. 그런 점에서는 메일 슬롯과 비슷하지만 **양방향 통신**이 가능하다는 장점이 있다.
    - 단 브로드 캐스팅이 불가능 하다.
- 메일 슬롯 (Mail Slot)
    - 브로드 캐스트 방식의 단방향 통신방식을 취하며, 메일슬롯에 할당된 주소를 기반으로 통신하기 때문에 관계없는 프로세스 사이에서도 통신이 가능하다.

## Anonymous Pipe
- 이름 없는 파이프는 옥상에 고인 물을 배수하기 위해 땅으로 연결 되어 있는 배수 파이프를 생각하면 된다.
```c++
BOOL CreatePipe(
    PHANDLE hReadPipe,
    PHANDLE hWritePipe,
    LPSECURITY_ATTRIBUTES lpPipeAttributes,
    DWORD nSize
)
```
- hReadPipe : 파이프 관은 입력 쪽과 출력 쪽이 있는데, 출력 쪽에 해당하는 핸들을 얻게 된다.
- hWritePipe : 파이프 관의 입력 쪽에 해당하는 핸들을 얻게 된다.
- lpPipeAttributes : 보안관련 정보를 전달 시 사용하게 된다. 핸들의 상속 특성을 지정할 때 사용하게 된다.
- nSize : 파이프의 버퍼사이즈를 지정하는 용도로 사용한다.
