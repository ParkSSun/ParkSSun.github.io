---
title: "System programming - section 5. 프로세스의 생성과 소멸"
date: 2019-07-30 00:13:00
categories: WindowsSystemProgramming
---
# 프로세스의 이해
---
## 프로세스란 무엇인가?
- 기존에 프로세스란 실행중인 프로그램을 프로세스라고 했다면 이제 좀 더 프로세스의 정체에 대해서 자세하게 알 필요가 있다.
- 예를 들어 우리가 어떤 프로그램을 실행시키기 위해서 실행파일을 클릭했다면 이때, 하드 디스크에 저장되어 있던 프로그램 관련 정보들 (메모리 구조 및 binary)가 메인 메모리로 올라갈 것이고 이 때 프로세스라고 한다.

## 프로세스를 구성하는 요소
- 프로세스를 구성하는 요소는
    - 1. Memory 구조(Heap, Stack, Data, Code영역으로 구성되는)
    - 2. Register Set
- 이렇게 프로세스별로 독립적으로 할당 받는 Resource는 프로세스를 구성하는 요소로 볼 수 있다.
- Process를 구성하는 범주는 Process가 독립적으로 할당받는 리소스이다.
- Why register set이 Process의 범주에 들어가는가?
    - 예를 들어 프로세스 A가 수행되고 있다가 프로세스 B가 수행되려고 하면 CPU는 1개이기 때문에, CPU의 Register Set을 프로세스 B에 맞게 변경이 되어야한다. 이 때, 프로세스A의 Register의 값을 저장 후 포로세스 B의 Register 값으로 Context Switching이 발생.
    - 이는 register도 process별로 관리가 되므로, register set이 process의 범주에 들어간다고 볼 수 있다.
    -
