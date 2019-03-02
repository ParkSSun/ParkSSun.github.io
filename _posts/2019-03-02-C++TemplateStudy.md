---
title: "C++Study-Function Template"
date: 2019-03-02 19:20:00
categories: c++
---
# FunctionTemplate
- c++은 여러가지 개발 방법을 지원하는 멀티 패러다임 언어라고 하는데 적어도 다음 세 가지 방법으로 개발을 할 수 있다.

| 구조적프로그래밍 | 객체지향프로그래밍 | 일반화프로그래밍 |
|---|:---:|---:|
| c언어에서와 같은 함수 위주의 프로그래밍 | 캡슐화, 추상화, 상속 및 다형성을 지원하기 위한 여러가지 언어적 장치를 제공 | 임의의 타입에 대해서 동작하는 함수나 클래스를 작성이 가능하다. |

## Template 이란?
- 무언가를 만들기 위한 **형틀** 이라는 것이다.<br/>
예를 들면, 붕어빵틀에 어떤 재료를 넣는가에 따라 다른 붕어빵이 나오듯, 템플릿은 어떤 인자를 넣는가에 따라서 다른 결과물이 나올 수 있다는 것이다.

## function template 이란?
- 함수 템플릿은 함수를 만들기 위한 형틀이라고 생각하면 된다.<br/> 비슷한 모양의 함수들을 여러개 만들어야 한다면 매번 직접 정의할 필요 없이 함수 템플릿을 만들어 놓고 이 템플릿으로 부터 함수를 찍어내면 되는 것이다.

```c++
#include <Turboc.h>
void Swap(int &a, int &b){
  int t;
  t=a;a=b;b=t;
}
void Swap(double &a, double &b){
  double t;
  t=a;a=b;b=t;
}
```
- 위와 같은 경우에는 여러개의 함수를 선언해야 하고 변수가 늘어날 때 마다 새로 추가해줘야 한다는 단점이 있다.
- 이를 해결 할 수 있는 방법에 대해 생각해 보자. <br/>
1. 인수 타입을 #define이나 typedef로 정의한 후 본체에서는 이 매크로를 참조하는 방법을 생각할 수 있다.
```c++
#define SWAPTYPE int
void Swap(SWAPTYPE &a, SWAPTYPE &b){
  SWAPTYPE t;
  t=a;a=b;b=t;
}
```

- 위와 같은 경우 컴파일 할 때 마다 define문을 바꿔서 컴파일해야 하는 문제가 있고, 여러 개 타입에 대해서 함수가 존재해야 할 때, 그럴 수 없다는 단점이 있다.

2. 매크로 함수를 쓰는 방법이 있다.
- 이 때 넘겨주는 값은 변수타입, 변수 값들이다.
```c++
#define SWAP(T, a, b){T t;t=a;a=b;b=t;}
```
-매크로함수를 쓰는 경우에는 인자로 type을 넘겨줘야 하는 문제가 발생하고, 이는 최소 의사 표시 원칙에도 맞지 않는다.<br\>
그리고, 매크로 함수의 경우에는 사용하는 곳이 많아 지면 그에 비례해서 크기가 증가하게 되므로, 이는 복잡한 코드에서는 문제가 될 수 있고, 매크로 함수가 가지는 기본적인 문제들이 있으므로
이 방법을 사용하지 않는 것이 좋다.

3. void 함수 포인터 사용 방법
```c++
void Swap(void *a, void *b, size_t len){
  void *t;
  t = malloc(len);
  memcpy(t,a,len);
  memcpy(a,b,len);
  memcpy(b,t,len);
  free(t);
}
```
위 와 같은 경우는 함수 한개만 가지고 메모리 값 자체를 교환하는 방식이므로 실용성이 있다고 볼 수 있으나, 길이를 매번 계산하여 넣어줘야 한다는 점에서 불편함이 있다고 볼 수 있다.

4. **템플릿 함수를 이용하자!**<br/>
 이 방법은 원하는 모양을 템플릿으로 등록해 놓는다면, 함수를 만드는 작업은 컴파일러가 알아서 한다.
```c++
template <typename T>
void Swap(T &a, T &b){
  T t;
  t=a;a=b;b=t;
}
```
  - 함수 템플릿을 정의하는 방법<br/>
  함수 템플릿을 정의할 때는 키워드 template다음에 <>괄호를 쓰고 괄호 안에 템플릿으로 전달될 인수 목록을 나열한다.
  ```c++
  template <typename T>
  template <class T>
  ```
  위 와 같이 typename, class둘 다 가능하나, class는 오해의 소지가 있어 class를 선언 할 때만 사용하고, class대신 typename을 사용하는 것이 좋다.<br/>
  템플릿 인수 목록이 두 개 이상의 타입을 전달받을 경우 다음과 같이 선언 할 수 있다.
  ```c++
  template <typename T1, typename T2>
  ```
## 구체화
---
- 함수 템플릿 정의문으로부터 앞으로 만들어질 함수의 모양만 기억하며 실제 함수가 호출될 때 타입에 맞는 함수를 작성하게 되는데, 이 때, 함수 템플릿으로부터 함수를 만드는 과정을 구체화 또는 인스턴스화라고 한다.<br/>
- 호출에 의해서 구체화되어야만 실제 함수가 만들어진다. <br/>
- 함수템플릿은 빵틀이고, 컴파일러는 어떤 형태가 될지만 알고 있다가, 실제 호출시에 인스턴스화가 발생하게 되는 것이다.<br/>
- 인스턴스화가 발생하여 함수 템플릿으로 부터 만들어지는 함수를 **__템플릿 함수__**라 한다.<br/>

| 함수 템플릿 | 템플릿 함수 |
|---|:---:|
|   함수를 만드는 템플릿 | 템플릿으로 부터 만들어진 함수 |

**참고**
| 배열 포인터 | 포인터 배열 |
|---|:---:|
|   배열들의 포인터 | 포인터를 담고 있는 배열 |

- 템플릿 함수들은 새로운 데이터 타입으로 인스턴스화 될 때 마다 새로 생겨나므로, 따라서 복사해서 함수들을 생성했을 때와 비교해보면 메모리에 이득은 없다라고 볼 수 있다.
- 장점은 새로운 데이터 타입을 처리하기 위한 함수를 생성을 컴파일러에게 위임하므로써 이득을 볼 수 있다.
- 만약 사용하지 않는 데이터 타입이 있다면 컴파일러에 의해서 생성되지 않을 것이기 때문에 사용자가 직접 복사하여 생성하는 것 보다 편리하고 메모리관리는 잘 될 것이다.

### 명시적 인수 지정

- 컴파일러는 호출 부의 실인수 타입을 판별하여 필요한 함수를 구체화하는데, 상수는 변수와 달리 그 형태만으로 타입을 정학하게 판단하기 힘든 경우가 있다.
- 따라서, 템플릿 함수를 호출 할 때는,실인수와는 다른 타입을 강제로 지정할 수 있는데,<br/> 코드는 다음 과 같다.
```c++
template <typename T>
T Max(T a, T b){
  return a>b?a:b;
}
Max(3,4) // int 로 처리됨
Max<double>(3,4) // double로 처리 됨
```
- return type이나 인수로 직접 사용되지 않는 타입을 가지는 함수를 호출하기 위해서는 명시적으로 템플릿 인수 타입을 지정해야만 한다.
- 그 이유는, 내부에서 사용되므로, 컴파일러가 호출 부의 실 인수 타입만을 가지고 타입을 결정 할 수 없기 때문이다.

```c++
template <typename T>
T cast(int s){
  return (T)s;
}

int main(){
  int i = cast<int>(234);
  double i = cast<double>(11);
}
```
- 위와 같이, 컴파일러가 호출부에서 실인수 타입만을 가지고 템플릿의 타입을 결정할 수 없을 때는 반드시 명시적 인수지정이 필요하다.
- 그렇다면 이렇게 명시적 인수 지정을 하는게 어떤 때 필요한지 알아보자.<br/>
```c++
template <typename T>
void LongFunc(T a){
    // 아주 긴 함수이다.
}

void main(){
  int i = 1;
  unsigned int k = 2;
  long j = 3;
  LongFunc(i);//이때 int 을 타입으로 가지는 LongFunc 생성
  LongFunc(k);//이때 unsigned int 을 타입으로 가지는 LongFunc 생성
  LongFunc(j);//이때 long int 을 타입으로 가지는 LongFunc 생성

}
```
- 위 의 코드를 보면 실제로 int/ unsigned int /long int나 동작하는 것이 비슷한데, 큰 코드가 3개나 생성이 됬다.
- 이는 Resource 낭비로 볼 수 있다.
```c++
void main(){
  int i = 1;
  unsigned int k = 2;
  long j = 3;
  LongFunc<int>(i);//이때 int 을 타입으로 가지는 LongFunc 생성
  LongFunc<int>(k);//LongFunc<int> 재사용
  LongFunc<int>(j);//LongFunc<int> 재사용
}
```
### 명시적 구체화 (Explicit Instantiation)
- 함수의 호출부를 보고 컴파일러가 템플릿을 알아서 만드는 것을 암시적 구체화라고 한다.
- 특정 타입에 대한 템플릿 함수를 강제로 만들고 싶다면 이때는 명시적 구체화를 해야 한다.
- 지정한 타입에 대한 템플릿 함수를 생성하도록 컴파일러에게 지시하는 것이다.
- 생성 방법은 다음과 같다. 일단 template 키워드 다음 함수 이름다음에 **생성하고 싶은 타입을 <>괄호 안에 넣는다.**
- 아래는 float 타입의 템플릿 함수를 명시적 구체화 한 것이다.
```c++
template void Swap<float>(float, float)
```
- 명시적 구체화 명령은 반드시 템플릿 선언 뒤에 와야 한다.
  - 어떤 template인지 알아야 컴파일러가 만들 수 있으니깐!

## 특수화 (Specialization)
---
- 템플릿의 경우 만들어진 함수는 타입만 제외하고는 동일한 본체를 가지므로 동작도 동일하다.
- 특수화는 **특정 데이터 타입에서 다른 동작을 구현하고 싶을 때, 사용하는 방법이다.**
- 예를 들어 Swap<double>함수인데, 내부에서 swap시 정수 부분만 swap을 하는 함수를 구현하고 싶다라고 하면 다음과 같이 특수화를 할 수 있다.
```c++
template <typename T>
void Swap(T &a, T &b){
  T t;
  t=a;a=b;b=t;
}

template<> void Swap<double>(double &a, double &b){ //Specialization
  int i,j;
  i = (int)a;
  j = (int)b;
  a = a-i+j;
  b = b-j+i;
}
```
- 특수화를 하는 다양한 방법 (**template 후 <> 괄호 사용 안하면 명시적 구체화 이므로! 주의**)

| Index | declaration | Note |
|---|:---:|---:|
| '1' | ``` template <> void Swap<double>(double &a, double &b)```  | '사용 바람직' |
| '2' | ``` template <> void Swap<>(double &a, double &b)```  | '사용 바람직' |
| '3' | ``` template <> void Swap(double &a, double &b)```  | '사용 바람직'|
| '4' | ``` void Swap<double>(double &a, double &b)```  |'함수명에 <> 있으므로 가능'|
| '5' | ``` void Swap<>(double &a, double &b)```  | '최신 표준에서는 인정 안됨'|
| '6' | ``` void Swap(double &a, double &b)```  | '그냥 일반 함수 Swap을 선언, 비추' |
| 'cf' | ``` template void Swap<double>(double &a, double &b)``` 명시적 구체화  | '명시적 구체화와 특수화 선언시 주의!'|
