---
title: "DDR4-TruthTableAnalysis"
date: 2019-03-09 00:18:00
categories: DRAM
---

# TruthTable Note 정리

1. 모든 DDR4 commend는 CS_n, ACT_n, RAS_n/A16, CAS_n/A15, WE_n/A14, CKE(rising Edge일 때)에 의해서 결정된다.
    - CKE pin 이 Low로 있으면 이 Pin들의 값은 의미없는 것 ?! No! Low 일 때의 Operation이 있다.
    - CKE Pin 이 Low로 있으면 의미가 있다(High State였을 때의 action이 무엇이였는지에 따라서 CKE Low일 때의 상태가 결정된다.)
    - CKE High -> CKE Low 떨어질 , Self Refresh Entry, Power Down Entry
    - 정리하면, CKE High -> Low 되면 2가지 CMD가 될 수 있는데, Self Refresh, Power Down Entry
    - CKE Low -> High 될 수 있는 건, SelfRefresh Exit, Power Down Exit
    - **가장 먼저 확인 해야 할 Pin은 CKE Pin**
2. BG/BA/RA/CA 의 MSB는 device density와 configuration dependent가 있다.
3. RAS_n/A16, CAS_n/A15, WE_n/A14 pin은 ACT_n pin에 따라서 Multi Function을 한다.
    - ACT_n pin이 High 일 때, RAS_n, CAS_n, WE_n pin 역할을 한다.
    - ACT_n pin이 Low 일 때, RAS_n, CAS_n, WE_n pin은 Address pin역할을 한다.
4. RESET_n은 어떤 기능을 하던지 항상 RESET_n은 High여야한다.
    - RESET_n은 Asynchronous pin이라 언제든지 Low로 내려 갈 수 있다.
    - RESET_n이 Low라는 것은 Device 꺼져 있다고 보면 된다?!
5. Bank Group address(BG), Bank Address(BA)는 어떤 Bank Group내의 Bank가 Open 될 지 결정한다.
    - MRS (Mode Register Setting) command의 경우 BG와BA는 Mode Register 위치를 정하는데 사용된다.
    - BG0, BA1, BA0를 이용해서 MR의 위치를 선택하는 것! BG1은 Always 0이다.
6. Burst Read/Write
    - Fixed/On the fly 일지는, MRS setting에 의해서 변경
    - Burst Read/Write는 중간에 Terminated될 수 없다.
7. Power Down Mode
    - 이 때는 진짜 Cell값 보장 해주지 않는다.
    - Why? SelfRefresh이런 동작들이 하지 않거든.
8. CTRL는 Self Refresh Exit 동작은 Synchronous로 보장해준다.
    - 즉, Self Refresh모드에서 나올 때, 돌다가 중간에 나오는 건 없다는 것이다.
9. ODT의 state는 Truth table에 영향을 미치지 않는다.
    - **Violation: SelfRefresh 동작 중에, ODT Function은 불가능 하다**
    - **Question: ODT Function?이 뭐임?**
10. VPP, VREF(VrefCA)는 SelfRefresh중에도 유지 되어야 한다.
    - VPP(external voltage pump) : DRAM Device가 내부 charge pump를 가지고 precharge voltage를 만드는 것을 완화시켜준다.
    - 즉, Precharge할 때(Row Open시) 필요한 voltage를 내부 Charge pump만 가지고 만드는게 쉽지 않으니, 이를 도와주는 역할
11. NOP command의 사용?!
    - Max Power saving mode Exit
    - Get Down Mode
    - 여기서 사용된다.
12. A17(Address 17번 pin)은 MRS 시, RFU이다.

# CKE Truth table note 정리

1. CKE (N) : current CKE clock Edge, CKE(N-1) : previous clock edge
2. Current state : clock edge N이전의 직전 state
3. COMMAND(N) : clock edge N때 등록된 command
4. ACTION(N) : COMMAND(N)의 결과
    - 해석해 보면 Current State였고, COMMAND(N)이 들어와서 ACTION(N)이 발생했다는 것.
5. 위 표와 Spec문서에 나와있지 않는 Sequence는 illegal이다.
6. CKE Transition에 대한 Timing 제약
    - **CKE Transition은 tCKEmin(CKE transition 이 다시 발생할 수 있는 시간)이 만족되기 전에 1nCK동안 유지되야 한다.**
7. SelfRefresh mode는 All Bank Idle State에서만 들어갈 수 있다.
8. Power Down Entry/Exit을 위한 Valid command는 DESELECT Only!
    - DESELECT : CS# high상태이고, 새로운 command가 excute되는 것을 막아준다. DRAM은 disabled
    - NOP : CS# low상태이고, 선택된 DRAM에 no operation을 수행하기 위해서 사용된다. 수행되고 있는 operation들은 영향을 받지 않는다.
9. SelfRefresh Exit를 위한 Valid command는 DESELECT만 된다.
    - **예외: GearDownMode & Max Power Saving exit의 경우 NOP도 사용가능**
10. Self Refresh mode는 read나 write state에서 들어갈 수 없다.
    - SelfRefresh는 위의 7번에서 말한 것 처럼 All idle state에서만 진입이 가능하다.
11. Power down 에서는 Refresh와 같은 동작 안해!
    - 위의 Truth table정리 7번 항목에 보면 됨.
    - 초기화 할 수 있는 값들은 가지고 있으나, cell값은 보장을 안하는 것으로.
12. X는 Don't care를 의미하고, 이는 SelfRefresh mode & Power Down mode에 해당한다.
13. Read/Write/Precharge의 command의 끝맺음에 있어서, **all banks들이 closed되었다면**, Precharge Power Down에 들어가진다. 그렇지 않을 경우에는 Active Power Down에 들어간다.
14. IDLE state가 정의 되려면?
    - all banks are closed
    - no data burst
    - CKE High
    - All timings from previous operation are satisfied
    - Self Refresh Exit and Power down exit 관련 Timing 들도 맞아야함(tXS, tXP)
