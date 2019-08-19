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
    
