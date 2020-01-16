---
title: "MoreEffectiveC++No.3"
date: 2020-01-15 22:44:00
categories: c++
---

# No3. 배열과 다형성은 같은 수준으로 놓고 볼 것이 아니다.

## 다형성이란?
- 다형성이란 의미는 여러개의 타입인 것 처럼 보인다는 것
- c++에서 사용 되는 다형성은, 기본 클래스와 파생 클래스가 있을 때, 기본 클래스의 포인터나 참조자를 이용해 파생클래스의 객체들을 조작할 수 있다.

## 다형성과 배열의 관계
- 결론적으로 다형성이 있는 클래스를 가지고 배열을 선언 시, 이는 매우 위험하다는 것.
- 즉, 다형성과 배열은 물과 기름과 같은 관계라고 생각해야 한다.

```c++
void deleteArray(ostream& logStream, BTS array[])
{
    logStrem << "Delete array" << endl;
    delete [] array;
}

BalancedBST *balTreeArray = new BalancedBST[50];

deleteArray(cout, balTreeArray);

```

- 다음 위와 같은 코드에서 delete[] array시 BTS::~BTS() 함수가 호 출 될 것이며 이는 메모리 누수로 이어진다.
- 포인터 연산을 하는 경우에도 다형성이 적용되는 함수 안에 파생 클래스를 넣어주는 경우 sizeof(파생클래스)가 아닌 sizeof(기본클래스)로 계산되어, 제대로 수행이 불가하다.
