---
title: "MoreEffectiveC++No.5"
date: 2020-01-20 22:44:00
categories: c++
---

# No5. 사용자 정의 타입 변환 함수에 대한 주의를 놓지말자.

## 스스로 만드는 타입에 대해서는 암시적 타입변환이 발생하는 것을 방치하지 말자.
- 컴파일러가 사용할 수 있는 타입 변환 함수는 두 가지 이다.
- 1. 단일 인자 생성자.
- 2. 암시적 타입 변환 연산자.

### 단일 인자 생성자.

```c++
class Name{
public:
    Name(const string& s);
};
```

-단일 인자 생성자는 인자를 하나만 받아서 생성되는 생성자 인데, 이 와 같은 경우에는 자동으로 string이 Name class로 형변환 가능하다.

### 암시적 타입변환 연산자.

- 암시적 타입 변환 연산자는 다음 아래와 같은 형태를 가진다.

```c++

class Rational{
public:
    operator double() const;
};

Rational r(1,2);
double d = 5*r;
```
- 위의 코드와 같은 경우, r이 자동으로 double로 형변환 후, 계산되어 진다.
- 이런 경우는 프로그래머들이 원하지 않는 경우들이다!

### 암시적 타입 변환을 막는 법

- **암시적 타입 변환 연산자의 경우. 다음과 같은 형태로 함수를 제공한다.**
  
```c++

class Rational{
public:
    double asDouble() const;
};
```

- **단일 일자 생성자의 경우, 다음과 같은 생성자를 만든다 (explicit keyword이용)**
```c++
class Name{
public:
    explicit Name(const string& s);
};
```

- explicit keyword 사용 시, 암시적 타입변환에 생성자가 사용되지 않는다.

