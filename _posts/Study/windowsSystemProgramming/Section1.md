---
title: "System programming - section 1. 시스템 프로그래밍의 이해와 접근"
date: 2019-07-23 00:13:00
categories: WindowsSystemProgramming
---
# 시스템 프로그래밍이란?
---
- 컴퓨터를 동작시키는 프로그램
- **하드웨어를 직접 Control하여 하드웨어를 사용할 수 있도록 도와주는 프로그램.**
- 대표적으로는 Windows / Linux가 존재
- But 요즘에는 System을 HW + OS까지 보는 경향이 있기 때문에, **Windows/Linux에서 제공해주는 라이브러리를 이용하는 것도 시스템프로그램이라고 한다.**
- Windows운영체제를 가지고 시스템을 control하는 프로그래밍을 하면, Windows 프로그래밍이라고 한다.

# 컴퓨터 하드웨어의 구성
---
## CPU(Central Processing Unit)
## Main Memory
- **컴파일이 완료 된 프로그램 코드가올라가서 실행되는 영역**
- 어떻게 사용되나?!
    - 예를 들어 우리가 어떤 프로그램을 다운 받았다고 하면 우선 이 프로그램은 하드 디스크에 저장 될 것 이다.
    - 하지만 우리가 이를 클릭하여 실행하게 되면 이는 메인 메모리에 올라와서 실행되는 것이다.

## Input/Output Bus
- **데이터를 주고 받기위해서 사용되는 경로이다.**
- 주로 고속도로에 비유 된다.
- 구성은 Address bus/ data bus/ control bus로 이루어져 있다.

# CPU의 이해
---
## ALU(Arithmetic Logic Unit)
- **ALU는 CPU내에서 연산을 담당하는 부분이다.**
- Register들로 부터 값을 받아오고, Control unit의 signal 및 clock pulse를 이용하여 계산을 수행한다.
- 주된 연산은 산술 연산 이나, 논리연산이다.
## Register
- **ALU에 들어가는 CMD나 피연산자 값들, 그리고 ALU의 결과 값들이 register에 저장된다.**
- Memory hierarchy에서 최상단에 위치한다고 보면된다.
## Control Unit
- CMD를 Fetch하고 이를 수행하면서, 발생하는 hazard를 control
- **즉 ALU가 연산하는데 발생할 수 있는 문제들을 해결해주는 역할을 수행한다.**
- hazard의 종류
    - structural hazard : 하드웨어적으로 cmd를 지원하지 못하는 경우
        - 예를 들어, 두 개의 명령억 같은 시기에 memory에 접근하려는데, memory I/O port는 한개이면 hazard가 발생하게 된다.
    - Data hazard : cmd를 수행 시 사용해야 하는 값이 있는데, 이 결과 값이 위의 파이프라인에 있는 cmd의 결과 값을 사용해야 하는 경우. 기다려야 하므로 hazard발생한다.
    - control hazard : branch prediction과 같이 조건에 의해서 다른 cmd를 수행하여 하는 경우. 이 때 pipeline에 들어와 있는 다음 cmd들을 필요없는 cmd들이 되는 것
    - 참고 : pipeline depth?! pipeline이 fully 활용되고 있을 떄, 몇 개의 cmd가 수행중인지 확인하면 된다!

## Bus interface
- 데이터를 전송하고 받을 떄, I/O bus를 사용하게 되는데, 이를 I/O Bus의 protocol을 알지못하면 사용하지 못한다.
-**따라서 CPU내에서 I/O bus의 통신방식을 이해하고 있는 그 무언가가 있어야 하는데, 이 것이 I/O Bus Interface이다.**
- **I/O bus에는 하드디스크, 그래픽 카트, 등 여러 장치들이 연결 되는데, 이 모든 장치들도 반드시 Bus interface가 필요하다.**
- 장치에 따라서 Bus interface가 아닌 controller, adapter로 불린다.
## Clock Pulse
- clock pulse는 cpu에서 발생시키는 것이 아니고 cpu에 제공되는 component이다.
- 예를들자면, 메트로놈이 있고 메트로놈이 발생시키는 소리에 맞춰서 모두 자신의 음을 내는 오케스트라가 있다고 생각하자.
- 이때 메트로놈은 clock pulse 이고, 이에 맞춰서 각자 자신이 내야할 음을 내는 사람들은 각각의 hw component에 이유 할 수 있다.
- 그럼 이런 clock pulse에 맞춰서(synchronous) 일을 해야 할 까?
    - 혼자만 일하면 상관 없지만 여러명이서 일한다고 할 때, 내가 한 일을 다음 사람이 받아서 일한다고 생각해보자.
    - 내가 일하는 속도와 다음 사람이 일하는 속도가 다를 것이다.
    - 다음 사람이 일하는 속도가 더 빠르다면, 내가 끝내야할 일이 끝나지도 않았는데 나한테 다음 결과 값을 요구 할 것이다. 이때, HW의 경우에는 buffer에 이상한 값을 다음 hw가 가져갈 수도 있다.
    - 따라서 clock sync를 맞추면 이에 따라서 모두가 일을 효율적으로 할 수 있는 것이다.

# 프로그램의 실행 과정
---
## 폰노이만 Architecture
- 폰노이만 구조는 stored program concept이라고도 한다.
- 즉, program은 메모리에 저장되어야 하고, 이를 순차적으로 실행되어야 한다는 것

## 프로그램 실행과정
- 프로그램이 실행되는 과정은 하드디스크에 저장된 프로그램이 메모리에 load되고, cmd 한줄 한줄이 bus를 통해서 cpu에 전달 되어 프로그램이 수행되는 것이다.
- 이때, cmd를 memory에서 불러오는 것을 Fetch(Step1)라 한다.
- fetch된 cmd가 어떤 cmd인지 확인하는 과정을 decodeStep2)라 한다.
- decode된 cmd를 수행하는 것을 excution(Step3)이라고 한다.
- memory에 접근해야 하는 경우에는 Mem단계(Step4)에서 수행된다.
- Write back(Step5)단계에서는 결과 값이 Register에 저장된다.
- 이 떄, memory에 저장되는 프로그램은 어떤 과정을 거쳐서 생성되는 것인지 알아보자.
    - step1. 프로그래머가 프로그래밍 언어로 프로그램을 한다.(예를 들어 c/c++,python으로 프로그래밍을 한다.)
    - step2. compile을 시키면 우선적으로 전처리기가 전처리지시자에 의해서 표현된 구문들을 처리한다.
    - step3. 컴파일러가 코드를 번역하여 assembly language로 번역한다/ 이때, symbol들도 생성된다.
    - step4. assembler가 assembly code를 번역하여 binary code를 생성한다.
    - step5. linker가 라이브러리나 함수들의 symbol을 참조하여 하나의 프로그램을 만들어 준다.
-
