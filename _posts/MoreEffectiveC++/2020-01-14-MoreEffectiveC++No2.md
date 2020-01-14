---
title: "MoreEffectiveC++No.2"
date: 2020-01-14 05:14:00
categories: c++
---

# No2. 가능한 C++ 스타일의 캐스트를 즐겨 쓰자.

## Cast의 종류에 따른 사용 목적 및 방법 정리

### 1. static_cast
- static_cast는 c 형태의 type casting과 가장 유사하다.
- 따라서 받는 제약도 같다.
- double 자료형을 pointer로 변경은 불가
- struct 자료형을 int로 변경을 불가 등

### 2. const_cast
- const와 volatile을 없애주는 친구이다.

### 3. dynamic_cast
- 클래스의 상속 관계에서 기본 클래스를 파생클래스로 변경 시 (다운 캐스팅떄 사용)
- 파생 클래스 간의 변경(sibling class간의 변경)

### 4. reinterpret_cast
- 함수 포인터 타입을 서로 바꾸는 경우에 많이 사용.
- 이식성이 떨어진다.(컴파일러마다 결과가 다르다.)
