---
title: "TDDStudy-Conference_DevolopingEasyCodeForTest"
date: 2019-04-26 20:20:00
categories: TDD
---
# presenter
- 정진욱
- http://jwchung.github.io/testing-oh-my
# Test하기 쉬운 코드란
- 같은 입력에 같은 출력이 나오는 코드 : Deterministic
- 외부의 영향을 주고 받지 않는 코드 : No side effects
    - 테스트 하려면 추가 데이터를 만들어야 한다.

# 테스트 코드 작성법

## Test하기 어려운 코드와 Test하기 쉬운 코드가 Mix case

- Solution : 분리하자!
- test하기 어려운 코드의 출력 값은 어떻게해?! 이를 input으로 입력 받자.

## Test하기 어려운 코드와 쉬운 코드는 어디서 만나야하나?!

- Test하기 어려운 코드가 제일 안쪽에 있다면 어려운 코드를 호출하는 상위 코드는 모두 테스트 하기 어려운 코드가 되어 버린다.
- Solution : 따라서 최대한 바깥쪽으로 테스트하기 어려운 코드를 빼낸다.


## 그럼 만나는 가장 자리는 어떻게 테스트 하나?!

### 수동테스트
- 이런경우는 자동화 테스트가 필요 없을 만큼 단순한 케이스

### 자동테스트
- test를 무조건 사용하도록 강제하려면??
- Mock을 사용한다.

| 행위 검증 | 상태 검증 |
| --- | --- |
| 행위가 호출되었는가   | 결과 값이 무엇인가?  |
| Mockist | classicist |
| 불필요한 추상화 유발 가능성 ??| 불필요한 추상화 필요 없음  |
| 구현되지 않은 코드에 의존이 가능  | 구현된 코드에만 의존 |
| outside-in 기법  | inside-out 기법 |
| 클라이언트 시각과 같은 방향  | 클라이언트 시각과 반대 방향 |
| 바깥쪽 method가 안쪽 method호출 따라서, 안쪽 method를 가상화 가능  | 안쪽 method 부터 확인하면서 나옴  |

- mock 사용의 문제점
    - mock사용을 남발할 가능성이 크다
    - mock사용수에 대한 답을 찾기 어렵다.
    - 상태 검증으로 돌아가보자.
- 이미 개발 된 코드는 ?
    - characterization이라는 방식을 이용하자.
