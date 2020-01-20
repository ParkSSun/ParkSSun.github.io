---
title: "MoreEffectiveC++No.6"
date: 2020-01-20 22:44:00
categories: c++
---

# No6. 증가 및 감소 연산자의 전위/후위 형태를 반드시 구분하자.

```c++
class UpInt{
public:
    UpInt& operator++();
    const UpInt operator++(int);
    UpInt& operator--();
    const UpInt operator--(int);

};

UpInt& operator++()
{
    (*this) += 1; 
    return *this;
}

const UpInt operator++(int)
{
    UpInt oldValue = *this;
    ++(*this);
    return oldValue;
}

```

- 증감 연산자의 전위 형태와 후위 형태는 서로 다른 타입을 반환한다는 것
- 전위의 경우 참조자 타입을 반환
- 후의의 경우 const 타입을 반환
- const가 되어야 하는 이유는 i++++과 같은 경우 i.operator++(0).operator++(0)과 같이 호출이 될 수 있다.
- 이는 이상한 것이므로, const type으로 반환되어야 한다.
- **효율 관점에서는 전위증감 연산자가 효율이 좋다**
- **원칙 : 후위증감 연산자는 전위증감연산자를 이용하여 구현하고 반환타입의 형태를 잘 알자**