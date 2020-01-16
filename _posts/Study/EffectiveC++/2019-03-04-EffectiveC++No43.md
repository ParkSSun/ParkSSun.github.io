---
title: "EffectiveC++No.42"
date: 2019-03-04 23:18:00
categories: EffectiveC++
---
# No43. 템플릿으로 만들어진 기본 클래스 안의 이름에 접근하는 방법을 알아두자.

템플릿으로 만들어진 기본 클래스가 있고, 이를 받는 상속 클래스가 있다면 상속 클래스의 함수 내에서 기본 클래스의 함수에 접근하는 방법을 알아야 한다.

```c++
template <typename Company>
class MsgSender{
public:

  void sendClear(const MsgInfo& info){
    std::string msg;
    Company c;
    c.sendCleartext(msg);
  }

};

template<typename Company>
class LogginMsgSender : public MsgSender<Company>{
public:
  void sendClearMsg(const MsgInfo& info){
    sendClear(info);
  }
}

```

- 위의 코드에서 sendClear함수는 호출 되지 않고 컴파일 에러가 발생한다.
- 문제는 compiler는 Company는 템플릿 매개변수 이므로, 이 템플릿 변수가 나중까지 무엇이 될지 알 수 없다. 따라서 MsgSender<Company>의 클래스가 어떤 형태인지 알 방법이 있을리가 없다.

```c++
template<>
class MsgSender<CompanyZ>{
public:
  // sendClear함수 제공하지 않음
}

template<typename Company>
class LogginMsgSender : public MsgSender<Company>{
public:
  void sendClearMsg(const MsgInfo& info){
    sendClear(info);
  }
}
```
- 위와 같은 완전 템플릿 특수화가 되어 CompanyZ 타입 일 때만 사용이 되는데, 이 class는 sendClear함수를 제공하지 않는다.
- 따라서, 컴파일러는 단지 위와 같이 sendClear() 함수만 선언하면 알 수가 없다.
- **컴파일러는 템플릿으로 만들어진 기본 클래스를 뒤져서 상속된 이름을 찾는 것을 거부한다.**

# Solution

1. 기본 클래스함수에 대한 호출문 앞에 **this->**를 사용한다.

```c++
template<typename Company>
class LogginMsgSender : public MsgSender<Company>{
public:
  void sendClearMsg(const MsgInfo& info){
    this->sendClear(info);
  }
}
```

2. **using 선언**을 사용한다.


- 이는 컴파일에게 sendClear함수가 기본 클래스에 있다고 가정하라고 알려준다.
- 이름 가려지는 것을 해결하는 것이아니라 컴파일러에게 기본클래스의 유효범위를 뒤지라고 알려주는 것.

```c++
template<typename Company>
class LogginMsgSender : public MsgSender<Company>{
public:
  using MsgSender<Company>::sendClear;

  void sendClearMsg(const MsgInfo& info){
    sendClear(info);
  }
}
```

3. 호출할 함수가 기본 클래스의 함수라는 점을 명시적으로 지정하는 것


- 이 방법은 명시적으로 한정시, 가상 함수 바인딩이 무시되기 때문에 비추!

```c++
template<typename Company>
class LogginMsgSender : public MsgSender<Company>{
public:ㅉ
  void sendClearMsg(const MsgInfo& info){
    MsgSender<Company>::sendClear(info);
  }
}
```
