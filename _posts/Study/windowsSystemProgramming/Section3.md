---
title: "System programming - section 3. 64 비트 기반 프로그래밍"
date: 2019-07-28 00:13:00
categories: WindowsSystemProgramming
---


# WIN32 VS WIN64
---
## 64비트와 32비트
- 64bit 컴퓨터와 32bit 컴퓨터의 차이는 두 가지에 의해서 결정된다.(N bit computer구조들까지 확장 가능)
    - 1. Data송수신 크기
        - 이는 I/O Bus width에 의존적
        - 즉 I/O bus가 data를 주고 받는 크기가 32bit라면 32bit 씩 data를 이동시키므로 32bit Architecture이다.
    - 2. Data처리 능력 by CPU
        - CPU가 한번에 읽어 들일 수 있는 명령어크기
    -**따라서, I/O BUS width와 CPU의 data처리 능력이 조합이 되어 64bit computer인지 32bit컴퓨터 인지 결정이 된다.**
    -예를 들어 I/O bus는 64bit system인데 CPU는 32bit system이라면? 64bit system이 아니다.

## 프로그래머 입장에서의 64bit컴퓨터
- 결론부터 말하자면 프로그래머 입장에서는 64bit system이면 더 좋다.
- 그 이유는 프로그래머가 접근하여 사용할 수 있는 메모리의 영역이 증가하기 때문이다.
    - 32bit system의 경우 pointer의 크기도 32bit이므로 4GB이 최대 접근 가능 영역
    - 64bit system의 경우 pointer의 크기도 64bit이므로 16TB이 최대 접근 가능 영역
    - 따라서, 프로그래머 입장에서는 사용가능한 메모리가 증가한다는 것 이므로, 이는 프로그램작성시 메모리 리소스에 대한 접근 가능영역이 증가한다는 것이다.
- Why? N-bit system은 N-bit pointer size를 가지는 것인가?
    - 1) Pointer의 크기는 크면 클 수록 좋다.
    - 2) 크면 클 수록 좋으나 제한은 Bus I/O Width이다.
        - Pointer의 크기가 128bit이고 Bus I/O는 32bit이라면,
        - Pointer 사용시 CMD 4번을 받아야 한다. 이는 성능 저하유발
        - 따라서, Pointer의 크기가 크면 클 수록 메모리 접근가능영역이 증가하므로 좋으나 성능을 고려한다면 I/O Bus Width가 최대치이므로 이런 제약 사항을 고려해야한다.
        - 위의 내용을 종합해보면 **64bit system은 64bit I/O Bus를 사용할 것이기 때문에 64bit Pointer가 되고, 32bit system의 경우 32bit I/O Bus를 사용할 것이기 떄문에 32bit pointer가 된다.

# 프로그램 구현 관점에서의 WIN32 VS WIN64
---
- 결론 : 32bit system과의 호환성을 생각하므로 큰 차이는 없으나 차이가 있다면 pointer의 크기가 64bit이 된다는 것이다.

## 64비트와 32비트 공존의 문제점
- 이 문제는 다음 예제 코드를 머리속에 넣어두면 된다.

```c++
int _tmain(){
    int arr[10] = {0,};
    int arrVal = (int)arr;
}
```
- 위의 코드를 보면 arr array의 pointer값을 (int)로 type casting하여 저장한다.
- 이는 32bit system에서는 괜찮았지만, 64bit system에서는 pointer가 64bit이므로 만약 arr[]의 address영역이 4GB이상이면 문제가 발생하게 된다.

## Polymorphic자료형
- polymorphic은 다형성이다. 어떤 상황에서는 A로 보이고, 어떤 상황에서는 B로 보이는 것을 의미한다.
- 즉 한 객체가 다양한 모습을 가지고 있어, 상황에 따라 그 모습을 달리하는 것을 의미한다.
- Polymorphic자료형은 자료형 이름은 같지만 64bit system일 때와 32bit system일 때, 그 기능을 달리하는 것을 의미한다.
```c++
#if defined(_WIN64)
    typedef __int64 LONG_PTR
    typedef unsigned __int64 ULONG_PTR
    typedef __int64 INT_PTR
    typedef unsigned __int64 UINT_PTR
#else
    typedef long LONG_PTR
    typedef unsigned long ULONG_PTR
    typedef int INT_PTR
    typedef unsigned int UINT_PTR
#endif
```
- 위의 코드를 보면 WIN64의 선언 유무에 따라서 그 형태를 달리하는 자료형이다.
- 이 것이 필요한 부분을 살펴보자.
```c++
UINT CalDistance(UINT a, UINT b){
    return b-a;
}
int _tmain(){
    INT val1 = 10;
    INT val2 = 20;
    _tprintf(_T("Position %u, %u\n"), (UINT)&val1, (UINT)&val2);
    _tprintf(_T("Distance %u\n"),
        CalDistance((UINT)&val1,(UINT)&val2));
}
```
- 위와 같이 coding시 WIN32에서는 잘 수행이 되겠지만 WIN64에서는 pointer가 64bit이므로 문제가 발생하게 된다.
- 해결책으로는 UINT32용 UINT64용 함수를 따로 만들고 전처리기로 두 함수를 분기태울 수 있겠지만, 이러면 코드만 너무 지져분해진다.
- 따라서 이때 사용할 수 있는 것이 Polymorphic 자료형이다.
- 위의 함수를 다음과 같이 바꾼다면 32bit / 64bit 시스템 모두에서 잘 작동할 것이다.
```c++
UINT_PTR CalDistance(UINT_PTR a, UINT_PTR b){
    return b-a;
}
int _tmain(){
    INT val1 = 10;
    INT val2 = 20;
    _tprintf(_T("Position %u, %u\n"), (UINT_PTR)&val1, (UINT_PTR)&val2);
    _tprintf(_T("Distance %u\n"),
        CalDistance((UINT_PTR)&val1,(UINT_PTR)&val2));
}
```

# 오류의 확인
---
## GetLastError함수와 에러 코드.
- Windows Error발생시 전역공간에 에러에 대한 정보를 Logging해놓는다.
- Error발생 시, 어떤 Error인지 확인을 하기 위해서는 Error를 Logging해놓은 전역공간에 접근을 해야한다.
- **이 때 사용되는 함수가 바로 GetLastError()함수이다.**
- 이 함수를 사용할 때 주의할 점은 Error가 발생 시 바로 GetLastError함수를 호출 해야 현재 발생한 Error에 대한 정보를 얻을 수 있다는 것이다.
- 만약 다른 System함수를 또 부르다가 GetLastError함수를 호출 시, Error 정보에대한 정확성은 보장 받을 수 없다.
