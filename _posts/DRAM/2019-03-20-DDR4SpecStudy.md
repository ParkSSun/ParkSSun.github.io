---
title: "DDR4-SpecStudy"
date: 2019-03-20 00:18:00
categories: DRAM
---
# 모르는 단어 정리

| Num | 단어 |  의미 | Note |
| --- | --- | --- | --- |
| 1  | TEN | Connectivity Test Mode Enable  | Normal Operation으로 가려면 Reset# 필요 |
| 2  | CRC | Cyclical Redundancy Check | JEDEC Spec에서 WriteCRC는 Write 시 Dram Ctrl가 CRC code를 만들고<br> 이를 Write Data 뒤에 함께 보낸다. <br> 이는 DRAM에 저장되지는 않고 DRAM이 데이터를 확인 후, CRC가 맞지 않는다면 후속 절차가 진행된다. <br> 후속절차로는 ALERT pin을 띠워서 지금 어떤 상황인지 알린다. CRC 는 Write bandwidth의 25%정도 차지한다.  |
