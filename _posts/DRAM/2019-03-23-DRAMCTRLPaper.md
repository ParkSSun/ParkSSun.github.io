---
title: "DDR4-CTRL paper review"
date: 2019-03-23 00:18:00
categories: DRAM
---

# Paper : Design of DDR4 SDRAM Controller
---
## function description of DDR4

- 8n prefetch 구조 이다. (이런 BL은 한 개의 bank에서 나온다.)
- 구조를 보면 bank group -> 4개의 bank -> 이 내부에 Row/ Col이 존재
- 4 bank group 구조 이고, 1개의 bank group내에 4개의 bank가 존재하므로 총 16개의 bank가 존재
- bank group을 사용함으로써 생기는 이점
    - bank group끼리는 I/O path 를 공유하지 않으므로 빠르게 동작이 가능하다.
- bank group내의 bank끼리는 I/O path를 공유하지 않으므로, 약간의 손해를 보는면이 있다.
    - 손해라는 것이 정확하게 어떤 것인가?
- Bank : Independent memory array
- 즉, bank단위로 타임이나 이런 것들이 관리 되어야 한다.
- **column단위로 관리되어야 할 타이밍 파라미터들 정리**
- **row 단위로 관리되어야 할 타이밍 파라미터들 정리**
- **bank단위로 관리 되어야 할 타이밍 파라미터들 정리**
- **bank group단위로 관리 되어야할 타이밍 파라미터들 정리**
- **rank 단위로 관리되어야할 타이밍 파라미터들 정리**
- page size : total number of column in Row
- Memory 동작 sequence는
    - Activate command와 함께 시작.
    - Activate command에는 항상 어떤 bank group/bank/row에 대한 정보가 들어있다.

## Architecture of DDR4 CTRL

### Rank Status machine

- 관리의 단위를 Rank별로 가져가야 겠네!! Yes!! Rank별로도 관리가 되어야되요!
- Bank management unit을 사용
    - 이게 뭐냐면 현재 bank의 state를 track하는 애임.
- 따라서 구조는 4개의 bank group status processor가 있고, bank group processor안에는 4개의 bank status processor가 존재한다.
- 각각 processor는 CMD Violation check하는 부분과, timing 을 count를 하는 카운터로 이루어져 있다.
- 가질 수 있는 status는
    - 1. Active
    - 2. readable
    - 3. writeable
    - 4. prechargeable
    - 5. row address??
- 이렇게 관리를 함으로써, read to write, write to read bus turnaround time, activate to precharge, activate delay between banks, banks group 간의 타이밍 관리가 된다.
- 예를 들어 bank 'x'가 idle 상태에서 activate cmd를 받은 경우, activate과 관련된 timing cmd는 tRRD(active to active delay), tRCD(active to read/write delay), tRAS(active to precharge delay)인데, 이와 관련된 timing counter가 동작하기 시작한다.
- 이때, 상태는 active = FALSE, readable = FALSE, writeable = FALSE, prechargeable = FALSE이다.
- tRCD가 지난 후의 상태를 보면 active = TRUE, readable = TRUE, writeable = TRUE, prechargeable = 0이다.
- DDR4의 경우 bank group이 도입되면서 tCCD(read to read / write to write delay)관련하여 tCCD_S/ tCCD_L의 개념이 들어왔다. 이유는?? bank group의 개념을 통해서 I/O path가 같이 쓰이지 않으므로 tCCD_s 사용이고, bank group내의 bank간에는 I/O path가 공유되므로 tCCD_L의 개념이 발생하였다.

### Command Address Latency (CAL)

- Command To Address Latency function은 Power Saving 목적으로 만들어졌다.
- 의미는 chip select과 Command/Address 사이의 delay이다.
    - Why? 사용하지 않는 Rank는 CMD/ADDRESS receiver를 꺼놓는다?
    - 그래서 command를 issuing하기 전에 DRAM은 receiver를 키기 위한 시간이 필요한 것이다.
    - 이 때, 기다려야하는 clock은 mode register에 있다.
    - 그럼 궁금한게 ChipSelect신호 들어가면 dram 은 cmd/address받기위한 receiver를 on하기위해서 기다리고 있다고 생각하면 되는거군!?
    - 그래도 정확하게 한번 다시 물어보자 CAL
        - Answer :

### Caribration unit

- Calibration Unit이 하는 것은 Write leveling, Read leveling, ZQ Caribration이 있다.

#### Write leveling
- CK와 DQS간의 skew를 잡는 것이다.
- CK와 DQS간의 Skew 발생 원인은 ?
    - Fly-by route구조로 가면서 CA와 CK는 fly by의 형태로 연결되고, DQS와 DQ의path는 fly by 구조가 아니게 되면서, DQS와 ck간의 skew가 발생하게 된다.
    - 그리고 DQ와 DQS는 각 data group마다 길이가 다르므로, 이를 calibration해야 한다.
    - 즉 CA와 각 data group의 DQS마다의 skew를 잡는 것이다.
- Why? DQS와 CK의 skew를 잡아야 하는 것인가?
    - PHY는 data를 보낼 때, clk이 언제 dram에 도착하는지 알아야 되!
    - **CA는 clk기반이지만 dq는 dqs기반이므로 둘의 align을 맞춰야 하는 것?**
        - Answer :
- Write leveling되면?
    - 각 dram의 dq와 CA사이의 delay를 계산하여, ca를 보내고 dq를 이용해서 data보낼 때, delay를 줘서 보내준다!

- 하는 방식은?
    - DRAM 내부의 phase detector를 이용하자.
    - 그냥 D-FlipFlop임
    - Flip Flop은 D Input은 clk이 들어가고, clk input에는 dqs가 들어간다. Q output에는 DQ pin이 연결
    - 따라서, 주기적인 clk값이 들어가고, DQS가 clk값으로 들어가는데 phy는 dqs를 pll/dll이용해서 값을 게속 보내면서 DQ값을 확인하여 0값이 오다가 1값이 오는 시점의 dqs delay의 값을 찾고 이를 저장해 놓는 것이다.
- 참고 : PLL Vs DLL
    - PLL (Phase Locked Loop) 과 DLL(Delay Locked Loop)는 둘다 입력과 출력사이의 위상차이를 compensation한다는 기능은 동일 하지만, PLL의 경우 입력 주파수보다 출력 주파수를 더 높게 만들 수 있지만, DLL의 경우 입력신호의 주파수와 출력 신호의 주파수가 항상 동일하다는 점이 차이가 있다.

#### Read Calibration

- What ? Read Calibration
    - DQS와 DQ간의 차이를 잡는 것이다.
    - DQS는 CK와 이미 Align 맞춰놨으니, DQ를 밀면서 값을 찾는 것인가?!
        - Answer :
- Why ?
    - ctrl phy의 경우 오는 dram(data group)에 따라 DQ/DQS 간 skew가 존재한다.
        - skew는 왜 발생해?
            - Read의 경우 dram이 dq와 dqs를 edge align시켜서 동시에 보내주지만, channel에서 skew가 발생이 가능하다.
            - fly by route 구조에서는 dram data group간에서도 skew가 발생이 가능하다.
            - 그렇다면 data group basis이고, dq도 dq by dq basis??
- How?
    - DDR1/ DDR2의 경우 pattern write and read and compare였다.
        - 이는 방식이 좀 모호하다.
        - 이유는? 일단 data를 write할 때 skew가 발생할 것이고, read할 때도 Skew가 발생할 것, lane to lane skew까지 생각하면 방식이 모호하다.
    - DDR3/DDR4의 경우는 predefined pattern(DDR3 1개/ DDR4 r개)를 MPR(Multi purpose register)에 LP2/LP3의 경우 mode register에 넣어놓고 이를 이용하여 이 pattern들을 읽어서 read calibration을 하는 것이다.
    - MPR쓰는 방법 알려줘!
        - MRS command이용 MR3[2]을 set 시켜서 enable시킨다.
        - MR3[1:0]을 이용해서 어떤 page를 읽을 것인지 select (Training pattern읽어야하니깐 page0 select해야 겠지!)
        - BA[1:0]를 이용해서 어떤 MPR을 읽을 것인지 select(Training  pattern은 Page0의 4개의 MPR 에 있음)

### ZQ calibration

- What?
    - off chip drive strength, ODT의 calibration은 external Rzq(External reference register)에 의해서 이루어 진다.
    - Rzq는 VSS와 DRAM device의 zq Pin사이에 연결되어 있는 Reference register라고 생각하면된다.
    - 크기는 240ohm
    - Rzq는 rank들 사이에서는 공유가능하다. 하지만 rank내의 dram package die 사이에서는 공유할 수 없다.
        - why?
            - zq calibration cmd는 한번에 한 rank내에 던져지지, 여러 rank에 한번에 내려지지 않으므로 rank별로는 공유가능하다.
            - 그러나 한 rank내에 있는 dram die 의 경우에는 동시에 cmd를 받게 되므로, 공유할 수 없는 것이다.
- Why?
    - ZQ Calibration 왜 하는 걸까?
    - Register로 실제 저항을 쓰는 것이 아니고 Transistor회로를 이용하여, 저항값을 결정. 따라서 여러가지 저항 값을 사용할 수 있다.
    - 하지만 Transistor는 PVT(Process, Voltage, Temperature)variation특성을 가진다. 따라서, ZQ calibration을 통해서 transistor의 PVT variation을 잡고 정확한 저항 값을 사용할 수 있다.
- How?
    - 외부의 Rzq에 흐르는 current와 같은 current가 흐로도록 transistor을 계속 On하면서 몇개의 transistor를 이용하여 하는지 확인.

## CMD Generation Unit
- CMD Generation Unit은 CMD Pipelining Unit으로 부터 cmd를 받아온다.
- CMD Generation Unit은 cmd를 보내 Rank의 status를 확인 한 후, 보낼 수 있는지 없는지 확인한다.
- 만약 보낼 bank의 status가 active status가 아니라면, active cmd를 보낸 후 tRCD이후에 write나 read cmd를 보내는 것이다.
- 새로운 cmd를 보낸 후에는 rank status를 update하게 된다.
- 여러개의 cmd 가 온 경우에는 look ahead way방식으로 cmd를 스케쥴링한다.

## Data Transfer Unit
- Data transfer unit은 Write FIFO/ Read FIFO로 구성되어 있다.
- Write FIFO의 경우 Host interface로 부터 data를 받아서 dram으로 data를 pass해주는 역할을 담당하고 있다.
- DDR4의 경우 BL8로 동작하게 되므로, 4clock을 이용해서 rising/falling edge에 각각 data를 전달해주게 된다. 이 때, CRC가 On되어 있는 경우 CRC parity bit도 추가되어서 보내진다.
- Read FIFO 또한 dram으로 부터 BL8단위로 data를 받으므로, 4 clock을 이용해서 dram으로 부터 data를 받게 된다.

## CRC Generator
- DRAM Speed 증가하면서, Write operation에 대한 reliability증가시키기 위해서 CRC(Cyclic Redundancy Check)를 사용한다.
- x8의 경우 64bit data + 8 bit CRC로 구성된다.
- 현재 DDR4에서는 ATM-8 HEC이라는 방식으로 CRC를 계산한다.

## Initialize & Refresh Logic
- reset pin low로 100us 유지, during intialize sequence동안
