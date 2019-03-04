---
title: "EffectiveC++No.42"
date: 2019-03-04 22:40:00
categories: EffectiveC++
---
# No42. typename의 두 가지 의미를 제대로 파악하자.

# class vs typename

```c++
template<class T> class Widget;
template<typename T> class Widget;
```

- 위 의 두 선언에서, class와 typename의 차이는 있을까?
  - Answer : 없다. 완전 동일 하다! 그런데, class는 class선언에서만 쓰는 것이 더 좋을듯! 그러니 typename 쓰자

# dependent name & nested dependent type name & non-dependent name

```c++
template<typename T>
void print2nd<const T& container){
  if(container.size() >= 2){
    T::const_interator iter(container.begin());
    ++iter;
    int value = *iter;
    std::out << value;
  }
}
```

 - dependent name
   - 위 의 코드에서 보면 template내에서 템플릿 매개변수인 T에 따라서 종속된 것을 가리킨다.
   - 여기서는 iter 가 타입이 T::const_interator로 템플릿 매개변수인 T에 따라서 달라지는 이름이다.
 - nested dependent type name
   - 의존이름이 어떤 클래스 안에 중첩되어 있는 경우 이를 중첩의존타입이름 이라고 한다.
   - C::const_interator는 중첩의존타입이름이다.(C container class 내의 iterator class가 중첩되어 있는 구조)
 - non dependent name
   - int와 같은 경우 템플릿 매개 변수가 어떠하던지 상관 없는 타입이름이다.
   - 이를 비의존이름이라고 한다.

# typename을 써야 할 때.

- **템플릿 안에서 중첩 의존 이름을 참조할 경우, 그 이름 앞에 typename 키워드를 붙여줘야한다.**

```c++
template<typename T>
void print2nd(const C& container){
  C::const_interator * x;
  ...
}
```

- 만약 C에 정적 데이터 멤버로 const_interator가 있고 x라는 전역변수가 있다면, 이러면 컴파일러는 이를 그냥 두 변수의 곱셈으로 처리할 것이다.
- 컴파일러는 이렇게 중첩의존이름의 경우 타입이라고 알려주지 않는 이상 그 이름이 타입이 아니라고 가정하게 되어 있다. 따라서 반드시 typename 키워드를 붙여줘야한다.

```c++
template<typename T>
void print2nd(const C& container){
  typename C::const_interator * x;
  ...
}
```

# "중첩의존타입이름에는 typename 키워드를 붙여줘야한다"의 예외!

- 중첩의존타입이름이 기본클래스의 리스트에 있을 때
  - public Base<T>::Nested
- 멤버 초기화 리스트 내의 기본 클래스 식별자로 있을 경우
  - Base<T>::Nested(x)
```c++
tempate<typename T>
class Derived: public Base<T>::Nested{
public:
  explicit Derived(int x)
  :Base<T>::Nested(x){

    typename Base<T>::Nested temp;
  }
private:
}
```

# typedef로 정의 할 경우
```c++
template<typename T>
void workWithIterator(Iter iter){
  typename std::iterator_traits<Iter>::value_type temp(*iter);

}
```

- 위의 코드에서 저렇게 긴 선언을 계속하려면 너무 힘들다.따라서 typedef로 선언을 해놓는다.
- 선언시 typename도 함께 선언해 줘야 한다.

```c++
template<typename T>
void workWithIterator(Iter iter){
  typedef typename std::iterator_traits<Iter>::value_type temp(*iter);

}
```
