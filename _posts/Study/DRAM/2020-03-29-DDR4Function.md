---
title: "DDR4 Function 정리"
date: 2020-03-28 00:18:00
categories: DRAM Platform
---

# DDR4 Function List
| Idx | Function | 제약 조건 정리 | Note |
| --- | --- | --- | --- |
| 1 | Write leveling | OK| |
| 2 | Temperature controlled refresh | OK | |
| 3 | Low power auto self refresh| OK | |
| 4 | Fine graualrity refresh| OK | |
| 5 | Multi purpose register| | |
| 6 | Data mask| | |
| 7 | Data bus inversion| | |
| 8 | TDQS| | |
| 9 | ZQ Calibration| | |
| 10 | DQ Vref Training| | |
| 11 | per DRAM Addressability | | |
| 12 | mode register readout| | |
| 13 | CAL | | |
| 14 | write crc| | |
| 15 | ca parity| | |
| 16 | controlled gear down mode| | |
| 17 | programmable preamble| | |
| 18 | maximumm power down mode| | |
| 19 | boundary scan mode| | |
| 20 | additive latency| | |
| 21 | 3DS | | |


## Write Leveling
### What?
- CLK와 DQS 사이의 Skew를 compensate하기 위한 training

### Why?
- Command, Address, Control signal은 Signal integrity를 위해서 T branch에서 fly-by Topology구조로 변경.
- 이렇게 됨으로써 CLK은 fly-by로 들어가나 DQS는 fly by 구조가 아니기 때문에, 둘 사이에 Skew가 발생하게 되었다.
- 따라서, clk와 dqs사이의 Skew를 compensate하기 위해 필요한 것이 Write leveling이다.
- 이때, 보상이 필요한 AC parameter 
    - tDQSS : tDQS rising edge to tCK rising edge
    - tDSS : DQS_t falling edge setup time to CK_T rising edge
    - tDSH : DQS_t falling edge hold time to CK_T rising edge

### How?
- Condition :
    - Write leveling enter : MR1 A7 High
    - Write leveling exit : MR1 A7 Low 
    - Write leveling mode일때는, DQS_T/DQS_C termination들은 Activated and Deactivated ODT
    - Dynamic ODT 는 Write leveling 할떄, 지원이 안되므로, MR2 A[11:9] bit = b'000

- Procedure Description
    - memory ctrl 가 MR1 A7을 1로 set하므로써, leveling mode 진입.
    - **Write leveling들어가고 나서는 DESELECT CMD/ MRS Qoff / MRS Exit write leveling만 유효한 cmd이다.**
    - Exit시에는 , MR1 A7을 0으로 Set하므로써 write leveling out
   

### Quesition 
 - **write leveling exit시(MRS MR1 A7 Low), 이떄, MR1 A1:A2  Output Driver Impedence CTRL, RTT_NOM, TDQS, QOFF등의 mr값이 변경될 수  있다.** 이거 무슨 말인지 모르겠다.
 - Write Leveling mode와 output buffer disable/enable의 조합의미

## Temperature controlled refresh mode
### what
- MR4 A3에 의해서 enable / disable되고, MR4 A2에 의해서 mode select가 support된다.(normal/ extended MR4 A2)
- Normal Temperature mode
    - MR4 A3 1, MR4 A2 0
    - tREFI 7.8us 
    - 이 때, 시스템은 DRAM이 85도를 넘지 않는 다는 것을 보장.
    - 45도 이하에는 내부에서 average periodic refresh interval을 조절하는 작업을 수행 (외부의 Refresh cmd를 일정한 비율로 skip)
- Extended temperature mode
    - MR4 A3 1, MR4 A2 1
    - tREFI 3.6us (85~95도)
    - 이 때, 시스템은 DRAM이 95도를 넘지 않는 다는 것을 보장.
    - 85도/45도 이하에는 내부에서 average periodic refresh interval을 조절하는 작업을 수행 (외부의 Refresh cmd를 일정한 비율로 skip)

## Low Power Auto Self Refresh
### what?
- Self Refresh 동작을 하는데, 온도에 따라 Refresh 주기를 Manual하게 설정해줘야 하지만, LPASR를 Enable시, DRAM이 알아서 온도에 따라 selfRefresh rate를 변경한다.

### why?
- Self Refreh들어갈 때마다, user가 manual하게 refresh operating range를 선택해줘야 한다. 이거 개귀찮스
- 잘 안맞음.. 재수 없음 data loss발생 가능

### How?
- MR2 A7:A6 = b'11 set

## Fine graualrity refresh

### What?
- Refresh cmd의 주기를 변경하는 것이 가능!
- 

### Why?
- ??


### How?
- MR3 A8:A6 의 bit set을 통해서 x1->x4까지의 Refresh cmd permitted되는 것을 정할 수 있다.
- **Temperature controlled Refresh mode가 Enable되어 있는 경우에는, Fine granualarity refresh는 Normal mode로 사용되어야 한다.**

### Question 
- Fine Granularity Refresh mode 왜써?


## Multi purpose register
### what
- 말 그대로 multi purpose register임.
- 사용처 1. DQ training with mpr
- 사용처 2. MR3 Definition
- 사용처 3. MPR Reads
- 사용처 4. MPR Writes
- 사용처 5. MPR Read Data format

#### 사용처1. DQ training with MPR

- MPR(1bit Register)중 4개는 DQ Bit pattern을 저장하는데 사용.
- 일단 MPR register에 DQ Bit pattern이 program되어 있는 상태에서 MPR Read cmd를 통해서 activate되면, link training동안 dq bus위에 MPR bits들이 올라가게 된다.
- 이떄, **제약사항 MPR Enable(MR3[A3] = 1)된 경우에는 MRS/RD/RDA/WR/WRA/DES/REF/Reset만 Support**가능함.
- **power down이나, self-refresh는 MPR mode내에서 지원 불가**.
- MPR Read 때는, RDA/WRA는 RD/WR과 동일한 CMD임. 즉 WRA던져도 내부에서는 auto precharge 무시한다.
- REF Cmd 발생 후 tRFC시간내에 다른 CMD는 Issue되어서는 안된다.
- x1Refresh cmd만 사용가능 함.
- MPR Read /MPR Write 는 refresh cmd 이전에 complete되어야 한다.


#### 사용처2. MR3 Definition

- MR3는 Trainning위해서 사용되어지는 MPR을 Control할 수 있다.

#### 사용처3. MPR Read
- 제약 조건 : 
    - MPR Read 는 BL8/BC4(FIXED) mode에서만 사용가능.
    - BC4 OTF mode에서는 사용이 불가하다.
    - Page 0 Read : AC Timing tCCD_S / tCCD_L 모두 사용가능.
    - Page 1/2/3/ : tCCD_L 사용
    - BC4 Fixed Read시 반드시 : tCCD_L 사용
    - DBI 는 MPR Read operation시 사용불가. 따라서, DRAM은 MR5 A12 RDBI Setting을 MPR시 무시. 따라서 우리는 따로 변경할 필요가 없다.
- Using sequence
    - DLL lock (If DLL Enable시,)
    - Precharge All
    - Wait Until tRP 
    - MRS MR3 
    - tMRD and tMOD 
    - Read CMD
    - RL 후 Data Out from MPR Location
    - Out시, MRS MR3 A2 = 0 set
    - tMRS and tMOD Wait
    - regular dram cmd use 가능.

#### 사용처4. MPR Write
- 제약 조건 없냐?
- Using Sequence
    - DLL Lock (If DLL is Enable)
    - Precharge all
    - tRP Wait
    - MR3 A2 = 1 MPR Enable
    - tMRD and tMOD Wait
    - Write CMD
    - Exit 시, MRS MR3 A2 = 0 Set
    - tMRD and tMOD wait
    - continue using regular DRAM CMD

### why
- Training 할때나, 각각 현재 값 확인 하고 싶을 때, 사용이 가능하다.

### how
 - MR3 Seetting 이용해서 MPR이 가능한데, Read는 page 0-3모두 가능
 - Write는 page0 만 가능. 
 - 즉 page 1-3 은 Read Only

### Question
- MPR Set 구성은?
- page 0/1/2/3은 뭐냐!?
- DLL Lock은 어떻게 하냐?

##

