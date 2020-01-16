---
title: "EffectiveC++No.41"
date: 2019-03-03 23:45:00
categories: c++
---
# No41. 템플릿 프로그래밍의 철릿길도 암시적 인터페이스와 컴파일 타임 다형성으로 부터

# 객체지향 프로그래밍
객체 지향 프로그래밍의 세계를 회전시키는 2개의 축
- Explicit Interface
  - 아래의 코드에서 doProcessing함수의 인수 w에 대해서 말할 수 있는 부분은 w은 Widget타입으로 선언 되었기 때문에, w는 Widget Interface를 지원해야 하고, Widget Interface는 소스코드(Widget타입이 선언된 header file)을 찾아보면 어떤 형태인지 확인 할 수 있으므로 이를 **명시적 인터페이스(Explicit Interface)**라고 한다.
- Runtime Polymorphism
  - Widget의 멤버 함수 중 virtual로 선언 된 것들은 runtime에 결정 되므로 이를 **runtime Polymorphism**이라고 한다.

```c++
class Widget{
public:
  Widget();
  virtual ~Widget();
  virtual std::size_t size() const;
  virtual void normalize();
  void swap(Widget& other);
};

void doProcessing(Widget& w){
  if(w.size() > 10 && w != someNastyWidget){
    Widget temp(w);
    temp.normalize();
    temp.swap(w);
  }
}
```


# 템플릿과 일반화 프로그래밍

객체지향 프로그래밍에서 강조되던 명시적 인터페이스와, runtime Polymorphism의 역할을 많이 줄어 들고, 템플릿과 일반화프로그래밍에서 강조되는 것은 **implicit interface & compile time Polymorphism**이다

- Implicit Interface
  - 아래 코드에서 T 타입이 지원해야 할 연산은 size, normalize, swap, 멤버함수를 지원해야 하고,<br/> 복사생성자(temp생성시) 및 부등비교와 같은 연산자도 지원해야 한다. 이런 몇개의 표현식은 T type이 지원해야 하는 **implicit interface**라는 것이다.
  - implicit interface라고 하는 이유는, 이는 선언이 된 부분을 확인 할 수 있는 것이 아니므로 암시적 interface라고 하는 것이다.

- Compile time Polymorphism
  - w가 수반되는 함수 호출 시, operator> , operator!= 와 같은 함수 호출시, 해당 호출을 성공시키기 위해서 템플릿의 구체화 또는 Instantiation이 발생한다. 이는 **컴파일 타임에 발생하고 템플릿 매개변수가 들어오느냐에 따라서 호출 되는 함수가 달라지고 이는 컴파일 타임에 결정 되므로 compile time Polymorphism이라고 할 수 있다.**

```c++
template <typename T>
void doProcessing(T &w){
  if(w.size() > 10 && w != someNastyWidget){
    T temp(w);
    temp.normalize();
    temp.swap(w);
  }
}
```

# Explicit Interface vs Implicit Interface
둘의 차이에 대해서 좀 더 확실하게 알아 보자.

## Explicit Interface

- 구성 : 함수 시그니처들로 구성
  - 함수 시그니처란?
    - 함수의 이름, 매개변수 타입, 반환 타입, 함수의 상수성 여부 등을 통틀어서 부르는 용어
    - cf) 멤버테이터는 시그니처에 들어 가지 않는다.
- 아래 Widget의 public interface는
  - 생성자, 소멸자, size, normalize, swap함수 + 암시적으로 컴파일러에 의해서 생성되는 복사생성자, 복사 대입 연산자도 포함 + 매개변수 타입, 반환타입, 함수의 상수성

```c++
class Widget{
public:
  Widget();
  virtual ~Widget();
  virtual std::size_t size() const;
  virtual void normalize();
  void swap(Widget& other);
};
```

## Implicit Interface

- 함수 시그니처에 기반하는 것이 아닌, 유효 표현식에 기반한다.
- 아래 template에 T type 에 요구되는 Implicit Interface는 다음과 같이 정리 할 수 있다.
    - size()함수이고 10과 비교할 수 있는 타입을 반환
    - operator!= 함수를 지원
    - w.size() > 10 && w != someNastyWidget의 결과 값이 bool type으로 반환 할 수 있도록 요구.
```c++
template <typename T>
void doProcessing(T &w){
  if(w.size() > 10 && w != someNastyWidget){
    T temp(w);
    temp.normalize();
    temp.swap(w);
  }
}
```

# Summary
- 클래스와 템플릿은 모두 인터페이스와 다형성을 지원한다.
- 클래스의 경우, 인터페이스는 명시적이고, 함수의 시그니처로 구성, 다형성은 runtime에 결정(by 가상함수)
- template의 경우 인터페이스는 암시적이고, 유효 표현식으로 구성, 다형성은 complie time에 결정( by template Instantiation)
