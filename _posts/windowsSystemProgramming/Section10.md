---
title: "System programming - section 10. 절차적 함수 호출(Procedure Call)지원 CPU모델"
date: 2019-10-05 00:15:30
categories: WindowsSystemProgramming
---

# 절차적 함수 호출 지원 CPU Model

## Stack frame의 구조
- stack frame?
    - 함수 호출 과정에서 할당되는 메모리 블럭을 (지역 변수들이 할당되는 영역) stack frame이라고 한다.
```c++
void fct1(){
    int c = 3;
    int d = -4;
}
```

- 위와 같은 코드가 있을 때, 변수 c,d는 stack에 할당되고 변수 c,d가 할당되는 메모리 블럭을 가리켜 stack frame이라고 한다.
- sp register?
    - stack pointer register라고 하며, stack은 데이터를 계속 쌓아 올리게 되는데, 이때 쌓아 올리는 위치를 기억해야 하고, 이를 기억하는 register를 sp register(stack pointer register)라고 한다.
- 이때, 발생할 수 있는 문제는?
    -  쌓아 올릴 때야 변수가 새로 생성되면서 그 크기만큼 계속 더해서 stack pointer의 위치를 변경 시키면 된다.
    - 하지만 function 이 return되어야 하는 시점에서는, 해당 함수의 stack frame의 크기를 알 수 없기 때문에 sp가 어디로 돌아가야 하는 지 알 수 없는 문제가 발생!
- Frame Pointer register
    - 그럼 sp register가 돌아가야할 위치를 누군가는 기억하고 있어야 겠네!!
    - 누가 할 것인가?! 이를 도와주는 것이 Frame pointer register이다.
    - Frame pointer register의 역할은, Stack pointer register의 back up역할
    -
