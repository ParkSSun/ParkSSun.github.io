---
title: "TDDStudy-Conference_TDDForQualityOfCode"
date: 2019-04-26 20:20:00
categories: TDD
---
# presenter
- 한상근

# TDD
## Cycle
- Red : write test that fails
- Green : Write code to pass the test
- Refactor : remove duplication

## Benefits
### Test자동화를 통해서 얻을 수 있는 장점
- 동작하는 코드에 대한 자신감
- 회귀 테스트를 통한 자유로운 리펙토링
- 리펙토링 : 외부적인 변경 없이, 내부의 코드의 기능을 변경.
- 코드에 대한 지식이 증가(characteristic test를 통한 legacy 코드에 대한 이해 증가.)
- 개발 생산성 향상
    - 1. test를 자동화를 통한 빠른 feedback.
    - 2. debugging하기 쉽다.(수동 테스트는 재현이 잘안된다.)
    - 3.
### Test first를 통해서 얻을 수 있는 장점
- 과도한 설계를 피하고
- 불필요한 기능을 줄임
- 실행가능한 문서를 가지고
- 코드 품질을 높인다.
- ex) sonar cube - 품질측정 툴

## SW품질
- External Quality + Internal Quality

### External Quality
- Release되고 나서 발견된 Defect

### Internal Quality
- 1. Coding Rules
- 2. Potential Bug
- 3. Comments
- 4. duplication
- 5. Complexity
- 6. Unit Test
- 7. Design & Architecture

#### code quality metric
| 구분 | 항목 | 설명 |
| --- | --- | --- |
| 복잡도 | Mccabe Cyclomatic Complexity(MCC)  | 실행경로 가지수 |
| 복잡도 | Cognitive Complexity  | depth에 대한 가중치가 발생 |
| 결합도 | Martin Metrics | 패키지의 안정성 및 추상화 상태 측정 |
| 결합도 | Depth Of Inheritance Tree(DIT) | 객체 상속 계층 구조성, 상위 객체 개수  |
| 응집도 | LCOM | 멤버 변수를 얼마나 사용하는가? |
| 응집도 | Specialize Index(SI) |   |

- MCC는 분기문의 갯수 : 주로 20 Under가 권고
- Martin Metrics는 Pan-in/ Pan out의 관계를 고려
    - Panout 높을 수록, 외부의 영향이 많음
    - Panin 높을 수록, 추상화가 되어야한다.

#### code quality case study
RodHilton, Quantitative evaluating Test Driven Development by Applying object oriented Quality Metrics to Open Source project
-Code Analyst

## Test Technique
- Unit Test / Mocking을 활용
- MockObject : 실제 객체가 아닌 가상의 흉내를 내는 객체로 테스트의 의존성 분리위해서 사용되는 test doubles 중 하나.

## Refactoring Smell
- refactoring.com/catalog/ 참고
- BDD(Behavior Driven Development)/ATDD(Acceptance Test Driven Development)
- 어떻게 Test해야 할 것을 뽑아낼 수 있을 까? 이를 BDD를 이용한다.
-
