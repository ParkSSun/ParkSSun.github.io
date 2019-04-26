---
title: "Timing Param정리"
date: 2019-03-23 00:18:00
categories: DRAM
---

# AC Param정리
- 정리 방식 : 우선 있는 것 다 써놓은 후 나중에 category화 해서 정리

| num | category | param | param meaning | note |
| --- | --- | --- | --- | --- |
| 1 | ModeReg | tMOD |  MRS command to Non MRS Command delay | DRAM이 feature들을 Update하는데 필요한 시간<br>NOP/DES cmd는 입력 가능 |
| 2 | ModeReg | tMRD | MRS comand to MRS command delay | MRS command 후 다음 MRS cmd까지 필요한 시<br> 이 시간은 some mode register 의 경우 address/command/control input functionality에 영향을 주므로 function update에 시간이 필요<br>NOP/DES cmd는 입력 가능 |
| 3 | Bank | tRCD | RAS to CAS Delay | ACT cmd 후 read나 write cmd가 오기 전까지의 delay<br>Row address를 열고 이를 Amplifier를 이용해서 증폭하는데까지 걸리는 시간. |
| 4 | Bank | tRAS | ACT to precharge Delay | Act 후 precharge cmd오기 전까지의 시간 |
| 5 | Bank | tPGM | hPPR Programming time | ppr Programming이 진행되는 동안 기다려야 하는 시간  |
| 6 | Bank | tPGMExit | hPPR Exit time | ppr exit하는데 걸리는 시간 |
| 7 | Bank | tPGMPST | new address setting time   |   |
