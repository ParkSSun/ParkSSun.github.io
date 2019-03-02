# Container
- 객체의 집합을 다룰수 있는 객체를 Container라고 한다.
- 예를 들면, 배열이나 연결 리스트 등의 객체를 저장하면 이런 객체들을 관리할 수 있는 기능을 가지는 또 다른 객체를 말한다.
- 유명한 동적 배열 템플릿으로는 vector와 같은 것들을 예로 들 수 있다.

# 템플릿 중첩
- 템플릿은 인수열에 들어 갈 수 있는 타입에는 특별한 제한이 없다. 기본타입부터 클래스 타입도 모두 인수열에 들어갈 수 있다.
- 즉, 타입이 되는 것들은 템플릿의 인자로 들어 갈 수 있다는 것이다.

```c++
template <typename T>
class posValue{
private:
  int x,y;
  T value;
public:
  posValue():x(0),y(0),value(0){ }
  posValue(int ax, int ay, T av):x(ax), y(ay), value(av){}
  void OutValue();
};
```
