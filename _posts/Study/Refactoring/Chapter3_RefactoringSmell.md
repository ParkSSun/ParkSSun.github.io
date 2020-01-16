---
title: "Refactoring_Chapter3_RefactoringSmell"
date: 2019-04-27 23:18:00
categories: Refactoring
---

# Types of RefactoringSmell
---
## Duplicated Code
- case 1. 한 클래스의 두 메서드 안에 같은 코드가 들어있는 경우.
    - Sol : method extraction 기법을 사용, 겹치는 코드를 빼내어 별도의 메서드로 만들고 그 메서드를 두 곳에서 호출
- case 2. 한 클래스의 두 하위 클래스에 같은 코드가 들어 있는 경우.
    - Sol : 중복 되는 method를 추출 한 후, 이를 pull up method기법을 적용하면된다.
- case 3. 코드가 똑같지 않고, 비슷하다
    - Sol : 메서드 추출을 통해서 같은 부분과 다른 부분을 분리한다. 템플릿 메서드 형성 기법을 적용
- case 4. 두 메서드가 알고리즘만 다르고 기능이 같다
    - Sol : 두 알고리즘 중에서 간단한 것을 택해서 알고리즘 전환을 적용.
- case 5. 중복코드가 메서드 가운데 있다
    - Sol : 주변 메서드 추출을 적용
- case 6. 서로 상관 없는 두 클래스 안에 중복 코드가 있을 때.
    - Sol : 중복 코드를 클래스 추출 방식이나 모듈 추출을 적용해서 제 3의 클래스로 떼어낸 후 다른 클래스에서 호출
---
## Long method
- method의 길이가 길수록, 사람들이 이해하기 어렵다.
- 따라서, 메서드를 과감하게 쪼갤 수 있어야 한다.
- 주석을 달아야 한다고 생각이 들면, 그 부분을 쪼개자.
- 메서드명은 수행하는 방식이 아닌, 수행하는 목적을 이름으로 만들도록 한다.
- 메서드에 매개 변수와 임시변수가 많으면 메서드 추출을 실시하기가 까다롭다.
    - Sol : 임시변수는 **메서드 호출로 전환, 메서드 체인으로 전환 기법**을 적용하면 임시변수가 제거.
    - Sol : 길게 열거된 매개변수는 **매개변수 세트를 객체로 전환 기법**, **객체를 통째로 전달 기법**을 적용하면 간단해 진다.
    - Sol :임시변수와 매개변수가 여전히 많다면 메서드를 **메서드 객체로 전환 기법**
- 조건문과 루프도 메서드로 빼야한다.
    - Sol : **조건문 쪼개기(Decompose Conditional)** , **루프를 컬렉션 클로저 메서드로 전환 실시 후 클로저 메서드 호출과 클로저 자체에 메서드추출** 실행.
---
## Large Class
- case 1. 기능이 지나치게 많은 클래스에는 엄청난 수의 인스턴스 변수가 들어있다.
    - Sol : Extract Class 방법을 사용. 하위 클래스로 적절하다고 생각이 되면, **Extract Subclass** 방법을 사용. 또는 대리자로 부적절 하다고 생각이 되는 경우, **Extract Module**방식을 사용하면 된다.
- case 2. 인스턴스 변수를 계속해서 모두 사용하지 않는 클래스의 경우
    - Sol : **Extract Class, Extract Module, Extract Subclass**
- case 3. 변수가 엄청나게 많이 있는 클래스
    - Sol : **Extract Class, Extract Module, Extract Subclass**
- case 4. 코드 분량이 너무 방대한 클래스
    - Sol : **Extract Class, Extract Module, Extract Subclass**
---
## Long Parameter List

- 데이터가 없을 때는, 이를 가져다 줄수 있는 메서드를 호출 하는 방식.
- 따라서, 매개변수가 많아 지면 않된다.
---
## Divergent Change

- Divergent change는 한 클래스가 다양한 원인 때문에, 다양한 방식으로 자주 수정이 일어나는 경우를 말한다.
- 즉,새로운 데이터 베이스를 생성할 떄마다, 3개의 메서드를 수정해야하고, 새로운 금융상품을 추가할 때마다 4개의 메서드가 추가로 생성되어야 하네, 이런 생각이 들면 하나의 클래스를 여러개의 변형 객체로 분리하는 것이 좋다.
---
## Shotgun Surgery

- Divergent Change와 반대로, 수정할 때 마다 여러 클래스에서 수 많은 자잘한 부분을 고쳐야 한다면 이 문제를 의심할 수 있다.
- 수정할 부분이 여기저기에 있다면 찾기도 힘들 뿐 아니라, 꼭 수정해야 하는 부분도 놓치기 쉽다.
- 따라서, 수정으로 변경되는 부분들이 있는 곳들을 모두 한 곳으로 모으는 방법을 선택
    - Sol : **Move Method, Move Field** 적용해서 수정할 부분들을 전부 하나의 클래스에 넣어야 한다.
- Divergent Change는 하나의 클래스에서 변경이 많이 발생하는 경우이고, Shotgun Surgery는 클래스 하나의 변화가 다른 여러 클래스에 영향을 미치는 경우이다.

---
## Feature Envy

- 객체의 핵심은 데이터와 그 데이터를 사용하는 프로세스를 한데 묶는 다는 점이다.
- case 1. 문제는, 어떤 메서드가 자신이 속하지 않은 클래스에 더 많이 접근(다른 클래스의 attribute에 더 많은 접근)을 한다면, 잘못된 소속의 구린내가 나는 것.
    - Sol : 더 많이 접근하는 클래스도 **Method Move**기법을 사용
- case 2. 메서드의 일부만 소속이 잘못된 경우
    - Sol : **Extract Method적용 후, Method Move 방법**을 적용

---
## Data Clump

- 데이터 항목은 뭉쳐서 돌아다니는 경우가 많다. 따라서, 이 경우에는 데이터 뭉치를 객체로 만들어야 한다.
    - Sol : **Extract Class, Introduce Parameter Object, Preserve whole object**
---
## Primitive Obsession
- 프로그래밍 환경을 구성하는 데이터는 기본 타입과, 레코드 타입이다.
- 레코드 타입의 경우, 의미있는 데이터를 묶어 구조화할 수 있다.
    - Sol : **Replace data value with object**, 데이터 값이 분류 부호 일 때는 **Replace Type Code with Class** , 조건문에 분류부호가 사용될 때는 **Replace Type Code with Subclasses, Replace Type Code with State/Strategy**
---
## Switch statements

- Switch문읜 단점은 반드시 중복이 생긴다는 점.
- 따라서, 새로운 코드가 추가 된다면 Switch문이 존재하는 모든 곳을 찾아서 바꿔야 하는 문제가 있다
    - Sol : **다형성, 즉 재정의를 이용**
    - 재정의를 넣을 위치는 어디가 되어야 하는가?
        - Sol : switch문에는 분류 부호가 흔히 사용되는데, 그럴 때는 분류 부호값이 들어 있는 메서드나 클래스가 있어야 한다. 이럴 때는 메서드 추출을 실시해서 switch문을 메서드로 빼내고, **method move**를 이용해서 그 메서드를 재정의해야 할 클래스에 옮기면된다.
        - 이 때, **Replace Type code with Subclasses, Replace type code with state/Strategy** 중 어느 것을 사용할 지 판단해야 한다.
        - 상속 구조를 만들었다면 **Replace Conditional with Polymorphism** 기법을 사용
---
## Parallel Inheritance Hierarchies

- 평행 상속 계층이란?
    - Sol :
---
## Lazy Class
- 비용만큼의 기능을 수행하지 못하는 비효율적인 클래스들은 없애야 한다.
- 기존에는 비용대비 효율이 좋았으나, 리팩토링으로 인해서 기능이 축소된 클래스, 또는 수정할 계획으로 작성되었으나, 수정을 실시하지 않아서 쓸모없어진 클래스가 직무유기 클래스에 해당한다.
    - Sol : **Collapse hierarchy, Inline class, Inline module**
---
## Speculative Generality
- 막연한 범용코드란? 조만간 이런 기능이 필요하겠구만 하는 막연한 생각에 아직은 필요 없는 기능을 수행하고 온갖 호출과 case문을 넣으려 하는 그 순간 막연한 범용 코드의 구린내가 풍긴다.
- 이 생각을 실천에 옮기면 대체로 코드를 알아보고 유지보수하기가 더욱 어려워짐.
    - Sol : 별다른 기능이 없는 클래스나 모듈이 있는 경우에 계층병합을 실시해야한다, 필요 없는 위임을 제거하려면 클래스 내용 직접 삽입을 실시, 메서드에 사용되지 않는 매개변수가 있으면 매개변수 제거를 실시, 메서드명이 이상하다면 메서드명 변경을 실시.
---
## Temporary Field
- 어떤 객체 안에 인스턴스 변수가 특정 상황에서만 할당되는 경우.
- **사실 잘모르겠다! 임시 필드가 뭔지는 나중에 다시 확인**

---
## message chain
- 메세지 체인은 클라이언트가 한 객체에 2의 객체를 요청하면, 제 2의 객체가 제 3의 객체를 요청하고, 제 3의 객체가 제 4의 객체를 요청하는 식으로 연쇄적 요청이 발생하는 문제점을 뜻한다.

---
## Middle Man
- 어떤 클래스의 인터페이스를 확인하니 절반이 넘는 메서드가 기능을 다른 클래스에 위임하고 있다
    - Sol : 이런 과잉 중개 메서드는 제거하고 원리가 구현된 객체에 직접 접근한다. 부수적인 기능을 가지고 있다면 **Replace Delegation with Inheritance**기법을 수행하여 중개 메서드를 실제 객체의 하위 클래스로 전환한다.

---
## Inappropriate Intimacy
- 클래스끼리 관계가 지나치게 밀접한 나머지 서로의 은밀한 부분을 알아내느라 시간을 낭비하는 경우.
    - Sol : **Move Method, Move Field**실시해서, 각 클래스의 관여를 줄인다.
    - Sol : **Change Bidirectional Associate to Unidirectional**기법을 적용할 수 있는지 판단 후 해당 클래스들이 공통으로 필요하는 부분은 별도의 안전한 클래스로 빼내면 됨
- 상속으로 인해서 지나친 관여를 발생시키는 경우. 즉, 하위 클래스가 항상 상위 클래스가 공개하는 것보다 많은 데이터를 필요로 하는 경우.
    - Sol : 상위 클래스에서 하위 클래스를 빼내는 경우. **Replace Inheritance with Delegation**기법을 적용.
---
## Alternative Classes with different interfaces
- 기능은 같은데 시그니처가 다른 메서드에는 메서드명 변경을 실시.
- 프로토콜이 같아 질 때까지 **Move method**을 실시한다.
- 단, 너무 많이 옮겨야 한다면, **Extract Superclass**을 실시한다.
---
## Incomplete Library Class
- 라이브러리의 기능이 부실할 경우 우리는 라이브러리에 기능을 추가해야 한다.
    - Sol : 추가해야 할 메소드가 2개 뿐이라면 **Introduce Foreign Method** 기법
    - Sol : 부가기능이 많을 때는, **Introduce Local Extention** 기법
---
## Data Class
- 데이터 클래스는 필드와 필드 읽기/쓰기 메서드만 들어 있는 클래스이다.
- **data class를 처리하는 방법에 대해서는 More Study TOOD**
---
## Refused Bequest
- 하위 클래스는 부모클래스의 메서드와 데이터를 상속받는다.
- 하지만, 상속받은 데이터나 메서드가 더 이상 필요 없거나, 쓰이지 않을 경우
- 문제는 잘못된, 상속구조에 있다.
    - Sol : **Push Down Method, Push Down Field** 사용해서 사용하지 않는 메서드와 데이터를 그 형재 클래스에 몰아 넣어야 한다. 상위클래스는 공통의 코드만 가지고 있게된다.
---

## Comments
- 불필요한 주석을 달지 말라는 것!
- 주석을 넣어야 겠다는 생각이 들때는 먼저 코드를 리팩토링해서 주석을 없앨 수 있는지 확인
- 어떤 코드를 넣은 이유나, 무슨 작업을 해야 할 지 모를 때, 잊기 쉬운 사항들은 주석을 적극 활용하는 것이 좋다.
