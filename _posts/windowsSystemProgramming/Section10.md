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
    - 즉, 돌아갈 위치의 sp register의 값을 fp register에 저장하는 것이다.
```c++
int main(){
    int a = 1;
    int b = 2;
    func1();
}

void func1(){
    int c = 3;
    int d = 4;
    func2();
}

void func2(){
    int e = 5;
    int f = 6;
}

```

- 위의 경우에 대한 stack memory구조에 대해서 확인 가정해보자
- 처음 frame pointer = 0, sp = 0
- 맨 처음, 0 번지에 a = 1저장, 4 번지에 b = 2 저장 (현재 sp = 8)
- func1 function call 시, 8번지에 frame pointer (0 값을 )저장
- frame pointer에 sp pointer 값(8)을 저장
- 따라서, 현재 sp pointer = 12, frame Pointer = 8 (main 과 func1의 경계 값)
- 다음 12 번지에 c =3, 16번지에 d=4 저장 (현재 sp = 20)
- func2 function call시, frame pointer에 있는 8 값을 stack에 밀어 넣고 (sp = 24)
- sp의 값을 frame pointer에 저장(frame pointer = 20, sp = 24)
- 다음 24번지에 e = 5, 28번지에 h = 6의 값을 저장한다.
- 함수에서 out할 때 이뤄지는 일 및 stack값의 변화에 대해서 알아보자.
- 현재 frame pointer는 20의 값을 가지고 있고, return 을 만나는 시점에 sp에 frame pointer의 값을 밀어 넣는다. (sp = 20) 그리고 20번지에 저장된 값을 frame pointer reg에 넣는다.(frame pointer = 8)
- func1이 끝나는 시점에서는 frame pointer 8값을 sp에 밀어 넣고 (sp =8) 이때, 8번지에 있는 값을 Frame pointer에 밀어 넣는다(frame pointer = 0)
- main으로 돌아와서 main이 끝나는 시점에 frame pointer는 sp 에 현재 값을 밀어 넣는다.(sp = 0)

- **Summary**
    - function call시, stack에 frame pointer 가지고 있는 값 저장 -> frame pointer에 현재 Sp값을 저장
    - function return시, frame pointer에 가지고 있는 값을 Sp에 이동 -> sp가 가리키는 stack영역에 있는 값을 frame pointer에 저장

# 함수 호출 에 의한 실행의 이동
- 함수의 호출 및 실행은 program counter 에 의해서 이루어 진다.
- 즉, PC 값이 증가하며 순차적으로 fetch, decode , excute 단계를 거치면서 동작이 이루어 진다고 알고 있으면 된다.
- 근데, 만약 분기를 타는 부분이 발생 시, 이를 어떻게 해야 할 것 인가?
- 이때는 PC값을 저장해 놓고 분기를 타는 부분으로 넘어가야 한다.
- **이를 위해서 사용하는 것이 lr register이다.**

# 함수 호출규약(calling convention)
- 함수 호출 시 전달되는 인자가 왼쪽 부터 오른 쪽의 순서로 stack에 쌓일 수도 있고 반대로 오른쪽 부터 해서 왼쪽 순서로 stack에 쌓일 수도 있다.
- 함수 호출과정에서 받은 stack frame을 정리해 주는 주체를 누구로 할 것인가 또한 정할 수 있다.
    - caller(함수 호출 부)에서 해주는 경우
    - function (호출 된 함수)에서 해주는 경우
- **함수 호출 규약은 함수 호출 시 인자를 전달하는 방식과 호출이 끝날 때, stack frame을 정리하는 방식에 대한 규약이다.**
