---
title: "System programming - section 15. 쓰레드 풀링"
date: 2019-11-09 18:00:30
categories: WindowsSystemProgramming
---
# 쓰레드 풀에 대한 이해
- 쓰레드 풀은 사용할 쓰레드 여러개를 미리 선언해 놓고 일이 들어 올때마다 쓰레드 새로 생성하지 않고 쓰레드 풀에서 꺼내서 일을 할당
- 일이 끝나면 반환하는 형대
- 장점 : 이를 통해서 쓰레드 생성, 소멸, 등록시간에 드는 시간을 없애서 성능향상을 만든다.
