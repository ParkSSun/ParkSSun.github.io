---
title: "MoreEffectiveC++No.1"
date: 2020-01-14 05:14:00
categories: c++
---
# No1. 포인터(pointer)와 참조자(Reference)를 구분하자.

## 포인터와 참조자를 사용함에 있어서 Rule이 있어야 한다.

### Reference를 사용하는 경우
1. 참조하는 대상을 이미 알고 있을 경우
2. 참조하는 대상이 바뀌지 않을 경우
3. Reference를 사용 시, 코드의 문법상의 의미가 이상해 지는 경우

#### '3. Reference를 사용 시, 코드의 문법상의 의미가 이상해 지는 경우'에 대한 보충 설명
- 만약 operator[]에서 Reference를 반환하지 않고, pointer를 반환하는 경우 다음과 같이 문맥이 이상해 질 것이다.
- pointer반환 하는 경우
```c++
vector<int> v(10);

 *v[5] = 10;

```

- 마치, v가 포인터의 벡터인것 처럼 보이게 하는 단점이 있다.
- Reference반환 하는 경우
```c++
vector<int> v(10);

 v[5] = 10;

```

### Pointer를 사용하는 경우
- 위의 Reference를 사용하는 경우를 제외하고는 pointer를 사용한다.
- 단, **pointer를 사용 시, 반드시 Null pointer 예외 검사와 같은 코드가 필요하다**
```c++
void printDouble(const double *pd)
{
    if(pd){
        cout << *pd;
    }
}
```
