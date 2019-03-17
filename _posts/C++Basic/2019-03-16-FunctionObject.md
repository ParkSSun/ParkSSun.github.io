---
title: "C++Study-FunctionObject"
date: 2019-03-16 23:20:00
categories: c++
---
# Function Object
---
## Function Object??Functor??
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
	- 아래 코드에서 functor를 입력으로 주고 같은 객체로 결과값을 받는 부분은 이상할 수도 있으나 Functor 전달의 경우 값의 의한 전달이기 때문에 가능하다.
	- 이를 통해서 중간결과나 계산에 필요한 보조 함수를 소유하는 것도 가능하면 필요한 모든 것들이 하나의 클래스에 캡슐화 되므로 재사용성 까지 확보된다.

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct accum {
	int sum;
	accum() { sum = 0;}
	void operator()(int a) { sum += a; }
};

void main() {
	int ari[] = { 2, 8, 5, 1, 9 };
	vector<int> vi(&ari[0], &ari[5]);

	sort(vi.begin(), vi.end());
	accum functor;
	functor = for_each(vi.begin(), vi.end(), functor);
	cout << "총합 : " << functor.sum << endl;

}
```
- 3. 멤버 뿐만 아니라 멤버 함수도 가질 수 있으며 생성자와 파괴자도 활용할 수 있다. 이를 통해서 생성자는 멤버의 값을 원하는 대로 초기화 할 수 있다는 장점이 있다.

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
using namespace std;

struct print {
	string mes;
	print(string m) : mes(m) {}
	void operator()(int a) {
		cout << mes;
		cout << a << endl;
	}
};

void main() {
	int ari[] = { 2, 8, 5, 1, 9 };
	vector<int> vi(&ari[0], &ari[5]);
	sort(vi.begin(), vi.end());
	for_each(vi.begin(), vi.end(), print(string("good")));
	for_each(vi.begin(), vi.end(), print(string("다르네!")));

}

```

- 4. **함수객체는 타입이므로** 템플릿의 인수로 사용될 수 있지만 함수 포인터는 단순한 값일 뿐이므로 템플릿의 인수로는 사용할 수 없다.
	- 즉, 함수객체는 타입이므로 컨테이너가 함수 객체를 소유할 수 있지만, 함수 포인터는 불가능 하다.
---

## 알고리즘의 변형

- for_each 함수는 순회 중에 할 일을 결정하기 위해서 반드시 함수 객체를 부르도록 되어 있다. 이 처럼 함수 객체를 명시적으로 요구하는 알고리즘도 있도 필요할 떄만 함수 객체를 옵션으로 받는 알고리즘도 있다.
- find의 경우 순회 중의 반복자의 값과 세 번째 인수로 지정한 값을 연산자로 비교하여 정확하게 일치하는 요소를 찾아낸다. 근데, 사용자가 원하는 방식으로 검색할 요소를 찾아야 할 경우가 있는데, 이때는 functor를 이용한다. 이때는 find_if를 사용한다.

```c++
Init find_if(Init first, Init last, UniPred F);
```
- UniPred는 functor가 단항연산자이고, predicate 조건자라고 부르는데, 요소가 지정 조건을 만족하는지를 검사하는 역할을 한다.

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <string>
using namespace std;

struct IsKim {
	bool operator()(string name) const {
		return (strncmp(name.c_str(), "김", 2) == 0);
	}
};

void main() {
	string names[] = { "김유신", "이순신", "성삼문", "장보고", "조광조", "신숙주", "김홍도", "정도전", "이성계" };
	vector<string> vs(&names[0], &names[9]);
	vector<string>::iterator it;
	it = find_if(vs.begin(), vs.end(), IsKim());
	if (it == vs.end()) {
		cout << "없네" << endl;
	}
	else {
		cout << *it << "이 있다." << endl;
	}
}

```
## 함수 객체의 종류
- 함수 객체가 하는 일은 비교, 대입, 합산등 알고리즘 구현 중에 필요한 연산을 처리하는 것이라고 할 수 있다.
- 함수 객체의 종류는 취하는 피연산자의 개수와 리턴타입에 따라 분류를 하게 된다.

| 인수의 개수 | bool이 아닌 리턴 값 | bool return |
| --- | --- | --- |
| 없음 | Gen | |
| 단항  | UniOp  | UniPred  |
| 이항  | BinOp  | BinPred  |

- Gen : Generator는 함수 객체를 생성하는 함수객체생성기라고 하는데, 입력 없이 혼자 무엇인가를 만들어 내는 역할을 한다.
- UniOp : 인수 하나를 취하는 단항 함수 객체이며 return type 은  bool이 아니다.
- UniPred : 인수 하나를 취하는 단항 함수 객체이며 return type은 bool이다.
- BinOp : 인수 두개를 취하는 이항 함수 객체이며, return type은 bool이 아니다.
- BinPred : 인수 두개를 취하는 이항 함수 객체이며, return type은 bool이다.
