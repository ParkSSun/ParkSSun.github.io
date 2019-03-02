---
title: "C++Study- Class Template"
date: 2019-03-02 20:20:00
categories: c++
---
# Class template
---
- 다음과 같은 코드는 class template을 사용할 수 있는 예시이다.
```c++
class PosValueInt{
private:
  int x, y;
  int value;
public:
  PosValue(int ax, int ay, int av):x(ax), y(ay), value(av){}
  void OutValue();
};

class PosValueDouble{
private:
  double x, y;
  double value;
public:
  PosValue(double ax, double ay, double av):x(ax), y(ay), value(av){}
  void OutValue();
};

class PosValueInt{
private:
  int x, y;
  int value;
public:
  PosValue(double ax, double ay, double av):x(ax), y(ay), value(av){}
  void OutValue();
};
```
- 위의 코드의 경우 각각 data type을 처리하기 위한 class가 별도록 존재하고, 나머지는 동일하므로 다음과 같이 하나의 템플릿으로 통일 시킬 수 있다.

```c++
template <typename T>
class PosValue{
private:
  T x, y;
  T value;
public:
  PosValue(T ax, T ay, T av):x(ax), y(ay), value(av){}
  void OutValue();
};
```
## class template Instantiation 하는 방법
```c++
void main(){
  PosValue<char> iv(1,1,2); // 클래스 템플릿으로 템플릿 클래스 instance 생성
  PosValue<double> cv(1.0, 1.1, 2.1);
}
```
- 위와 같이 **클래스 템플릿**을 이용하여 **템플릿 클래스를** 생성하였다.
- 템플릿 클래스 선언 시 항상 타입이 명시 되어야 한다.
    - 그 이유는, 명시되어있어야 해당 클래스를 위한 메모리 영역 할당이 가능하기 때문이다.
- 즉, char형으로 생성된 클래스의 타입은 **PosValue<char>** 이다.

## class template의 멤버함수 작성방법
- 클래스 템플릿의 멤버함수를 선언문 외부에서 작성시에는 템플릿에 속한 멤버임을 밝히기 위해서 소속 클래스의 이름에도 <T>를 붙여야 한다.
- 템플릿 인수임을 명시하기 위해서 template <typename T>가 제일 먼저 와야 한다.
```c++
template <typename T>
void PosValue<T>::OutValue(){ ... }
```

## template class의 상속 시, 선언 방법
- base class에 <>괄호를 사용하도록 한다.
- 템플릿 클래스가 다른 클래스의 기반 클래스로 사용되면 컴파일러는 클래스를 즉시 구체화 한다.
```c++
class PosValue2 : public PosValue<int> {...};
```

## template으로 선언 되지 않은 일반 클래스 내에서 특정 멤버 함수만 template화 하는 것도 가능하다.
- 멤버함수 중에서 타입에 따라서 **여러개의 함수를 overloading해야 한다면, 이를 template화 하는 것도 가능**하다.
```c++
class Some{
private:
    int Mem;
public:
    Some(int m):Mem(m){}
    template <typename T>
    void memFunc(T a){
        ....
    }
};
```

## Template의 위치
- 템플릿 선언문은 반드시 사용하기 전에 위치해야 함
    - 그 이유는, 선언이 사용하기 전에 오지 않으면 컴파일러가 template을 들고 있지 않으므로, 객체를 생성할 수가 없다.
- 함수 본체의 정의(선언 x)를 사용한 후에 있어도 상관은 없다.
- **클래스 템플릿은 선언문과 멤버 함수의 정의까지 모두 헤더 파일에 작성되어야 한다.**
    - 즉, template의 경우에는 선언과 구현이 같은 번역단위에 있어야 한다는 것이다.
    - 일반적인 class의 경우, 멤버함수는 선언에 있으므로 컴파일 타임에 링크 되는 것이 아니라, 링크 타임에 연결 된다.
    - template class의 경우 컴파일 타임에 구체화 하므로, 따라서 선언과 구현이 같은 번역 단위에 있어야 하는 것이다.
- 이렇게 같이 선언 및 구현이 되도, 실제로 생성되는 것이 아니라 선언만 되어 있는 상황이고, 실제로 객체가 생성되는 시점에 메모리에 잡히는 것이다.
- 그럼 헤더 파일에 다 구현이 되면 소스 파일이 사용자에게 공개 되는 것 아닌가요?!?!?
    - 이에 대한 해결책으로 최신 c++ 표준에서는 export 키워드를 도입하여 이 키어드를 사용하면 구현 파일에 정의된 멤버함수가 외부로도 알려지도록 한다.
    - 템플릿 선언에 export를 붙이면 된다.
    - 일부 컴파일러만 지원하고 있다.
        - c++ 컴파일러는 번역 담위별로 컴파일하여 링크타임에 합치는 방식을 사용하는데, export로 지정되어 있는 함수에 대해서는 모든 번역 단위에 대해서<br/>정의를 알려야 하므로 근본적으로는 컴파일 방식을 바꾸기가 어렵다.
```c++
export template <typename T>
void PosValue<T>::OutValue(){...}
```

## NonType Argument
- 보통 템플릿에 전달 되는 것은 타입이다.
- Nontype Argument는 타입이 아닌 상수를 템플릿 인수로 전달할 수 있는 것을 말한다.

```c++
template <typename T, int N>
class Array{
private:
    T ar[N];
public:
    void SetAt(int n, T v){ }
    T GetAt(int n){ }
};
void main(){
    Array<int, 3> tt;
    tt.SetAt(1,3);
}
```

- 위 와 같은 코드에서 정수 상수가 템플릿의 전달되는 인수이므로 비타입 인수라고 한다.
- 문제는 크기가 다른 객체를 선언할 때 마다, 클래스가 구체화 되므로 이 때문에 멤버함수도 전부 따로 만들어진다. 따라서 메모리의 낭비가 심하다.
- 주의 할 점은, 비타입 인수는 반드시 상수여야 한다는 것이다. runtime에 결정되는 변수가 인수로 들어가서는 안된다.

```c++
int size = 5;
Array<int, size> tt; //불가능 하다.

const int size = 5;
Array<int, size> tt; //가능하다.
```

## 디폴트 template 인수
- 선언시 <>괄호 속에 타입을 지정해 주지 않아도 자동으로 들어가는 타입을 밀리 설정해 놓을 수 있다.

```c++
template <typename T = int>
class PosValue{
   ...
};

void main(){
    PosValue<> tt;
}
```
- **주의점**
    - type은 지정하지 않더라고 <> 괄호는 반드시 있어야 한다.
    - 여러개의 타입이 있을 경우, 오른쪽 인수부터 차례대로 default type으로 지정 할 수 있다.
    - 함수 템플릿에는 default 를 정의 할  수 없다.
        - class의 경우 객체를 선언 할 떄, 클래스 타입을 지정하므로 생략 가능
        - 함수는 호출 시 실인수 타입을 보고 구체화 할 함수를 결정하는데, 실인수가 생략되어 버린다면 어떤 타입을 원하는지 컴파일러가 알 수 없다.
## 명시적 구체화 (Explicit Instantiation)
- 어떤 특정 타입에 대해서 미리 class선언을 만들어 놓으려고 하는 경우 사용
- compiler가 해당 클래스의 객체를 생성하는 것이 아니고, 클래스를 생성하는 것이다.

```c++
template class PosValue<float>;
```

## 특수화(Specialization)
- 특정 타임에 대한 클래스를 따로 생성하는 것
- 예를 들어 기본 타입(int, double, char...)등에서는 지원이 가능하나, class나 structure와 같은 타입에서는 동작이 달라지는 경우 해당 타입에 대해서만 구체화가 가능하다.

```c++
template <typename T>
class PosValue{
private:
  T x, y;
  T value;
public:
  PosValue(T ax, T ay, T av):x(ax), y(ay), value(av){}
  void OutValue();
};

struct tag_Friend{
  char name[50];
  int age;
  double height;
};

template<> class PosValue<tag_Friend>{
private:
    int x,y;
    tag_Friend value;
public:
    PosValue(T ax, T ay, T av):x(ax), y(ay), value(av){}
    void OutValue();
};
```
- 위 와 같이 tag_Friend 타입에 대한 class 특수화를 하였다.
- 사실 제일 좋은 것은 tag_Friend를 int와 같이 사용할 수 있도록 하여 Specialization을 하지 않는 것이 제일 좋다.
- **Specialization을 선언하는 방법**

```
template<> class 클래스명<특수타입>;
```
- 특수화를 하게 되면 클래스는 객체를 선언하지 않더라도 자동으로 구체화된다.
- 즉, 클래스의 정의가 만들어지고 멤버함수들은 컴파일 되어서 실행파일에 포함된다.
- ** 따라서, 특수화된 클래스에 대한 정의는 일반적인 템플릿 클래스와는 달리 헤더 파일에 작성되어서는 안되고 구현 파일에 작성해야 한다 **

## 부분 특수화 (Partial Specialization)
- 템플릿의 인수가 여러개 있을 때, 그 중 한개에 대해서 특수화를 진행하려고 하는 기법이다.

```c++
template <typename T1, typename T2> class SomeClass{...};
```
- 위 와 같은 클래스의 경우 <int, int> , <int, double>, <double, int>등 다양한 조합이 가능하다.
- 부분 특수화는 이 중 한개의 타입에 대해서 강제로 지정하고 싶을 때 사용한다.

```c++
template<typename T1> class SomeClass<T1, double>{...};
```
