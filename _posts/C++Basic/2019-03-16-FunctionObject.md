---
title: "C++Study-FunctionObject"
date: 2019-03-16 23:20:00
categories: c++
---
# Function Object
---
## Function Object??
- **함수 객체는 클래스안에 함수를 캡슐화해 놓은 것으로 함수 포인테에 대한 일반화!**
- function object는 STL에서 알고리즘의 기능을 대폭향상 시키기 위한 역할을 한다.
- STL에서 알고리즘이 각 자료 타입들의 특정 기능을 수행한다면, 그 기능을 수행하면서 타입마다 다르게 처리하거나 해야하는 부분은 function object를 사용하여 기능을 추가적으로 사용할 수 있다.
- Function object의 대표적인 예로 for_each 함수를 보자

```c++
UniOp for_each(Init first, Init last, UniOp op);
```

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

void print(int a){
	cout << a << endl;
}
void main(){

	int ari[] = { 2, 8, 5, 1, 9 };
	vector<int> vi(&ari[0], &ari[5]);
	sort(vi.begin(), vi.end());
	for_each(vi.begin(), vi.end(), print);
}
```

- 여기서 for_each함수의 세 번째 인수로 전달되는 대상을 **함수 객체(Function Object)라고한다**
- **함수 객체는 함수 포인터 뿐만 아니라 () operator가 정의되어 있는 모든 객체이다.**
---
## 함수객체를 사용하는 이유?
- 1. 함수객체는 인라인이 가능해서 처리 속도를 대폭적으로 개선할 수 있다.
    - 즉 함수 객체가 아닌 함수포인터로 전달 되는 경우, 호출 시 마다, 스택프레임을 만들어야 하고 이를 저장하고 하는 과정이 매번 수반 되지만, 함수객체의 경우 선언되는 부분에 정의되어 인라인화가 되어 이런 스택프레임을 만드는 과정이 수반되지 않는다.
- 2. 함수객체는 객체이므로, 함수연산자() 뿐만아니라 처리에 필요한 멤버들은 추가로 더 가질 수 있다.
```c++



```
