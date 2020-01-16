---
title: "System programming - section 14. 쓰레드 동기화 기법2"
date: 2019-11-09 17:20:30
categories: WindowsSystemProgramming
---
# 실행순서에 있어서 동기화

## 생산자/소비자 모델
- 실행 순서의 있어서의 동기화는 생산자/소비자 모델로 모델링이 가능하다.
- 즉 소비자가 어떤 것을 소비하기 위해서는 생산자가 먼저 물건을 만들어야 소비가 가능하기 떄문이다.
- I/O model에 생산자/소비자 모델을 적용하면, 생산자는 input을 쓰레드에 전달 하고 input thread는 이를 buffer에 쓴다. 그리고 output 담당 쓰레드는 buffer에서 값을 가져다가 이를 처리하는 구조이다.

## 이벤트 기반의 동기화
- 생산자가 일을 다했는지는 생산자 쓰레드가 안다.
- 생산자는 일을 다했는지 소비자에게 알려주기 위해서 kernel object를 생성한다.
- 이 kernel object는 처음에 non-signaled상태이다.
- 소비자는 WaitForSingleObject 함수를 호출하면서 Non-signaled상태에서 signaled상태가 되도록 기다린다.
- 생산자는 생산을 다한 후 non-signaled상태를 signaled상태로 변경시키면 소비자는 signaled된 것을 확인하고 사용가능하다.
- 이때, signaled -> Non-signaled상태로 변경을 어떻게 하는지에 따라서 2가지 모드로 나뉜다.
    - manual-reset mode
        - manual reset mode는 signaled mode 에서 non signaled mode로 자동으로 변경된다.
        - 즉, 소비자쪽에서 WaitForSingleObject함수에서 blocking되어 있다가 빠져나오게 되는 순간 signaled -> non signaled로 변경된다.
    - auto-reset mode
        - manual reset mode는 signaled mode 에서 non signaled mode로 수동으로 변경된다.
        - 즉, 소비자쪽에서 blocking되어 있다가 빠져나오게 되는 순간 ResetEvent 함수를 호출하여 signaled -> non signaled로 변경된다.

## manual reset mode event의 활용 예
- 동시에 여러개의 쓰레드를 꺠워서 일해야 하는 경우에는 아주 좋은 방법이 될 수 있다.

## event + mutex 조합
- 많이 사용!!

## Timer 기반의 동기화
- Timer를 사용하여 signaled상태로 바꾸겠다는 것이다!
- 수동 reset Timer
- 주기적 타이머
