title: "2020sTargetAndFeedBackPlan"
date: 2020-01-17 23:45:00
categories: Study
---
# Target
- 매일 매일 조금 씩이라도 하는 것
- 공부의 각 Category정해서 얼마나 하고 있는지!
-
## Study Category
- DRAM
- Programming
- Photo
- Excercise
- BigData & AI

### DRAM
- what
  - DDR5 Study
  - LPDDR4 Study 
  - DDR5 CMD Parser
  - LPDDR5 CMD Parser
  - DDR5 AC Timing Param Checker
  - LPDDR5 AC Timing Param Checker
  - DDR5 Spec Violation checker
  - LPDDR5 Spec Violation checker
- why
  - 차세대 DRAM 에 대해서 알아야 함
  - 먼저 알고 이를 구현하고 테스트 할 수 있는 환경을 준비해놔야함
  - DRAM 파트에 있는데 DRAM을 모른다는 건 말이 안됨
- how
  - DDR5 Spec먼저 공부
  - DDR5 CMD Parser 구현
  - DDR5 AC Parameter checker 구현
  - DDR5 Spec violation checker 구현 
  - LPDDR5 Spec먼저 공부
  - LPDDR5 CMD Parser 구현
  - LPDDR5 AC Parameter checker 구현
  - LPDDR5 Spec violation checker 구현 

### Target
- DDR5 관련 Program 만들기
- LPDDR4 관련 Program 만들기
#### DRAM Study Table
| Index | Study 항목 | 왜 해야 하는가  | 어떻게 할 것 | 일정 | FeedBack | 보상 |
| ---| --- | --- | --- | --- | --- | --- |
| 0 | LPDDR5 Spec Study  | Spec을 알아야 Program만들지  | Spec먼저 훅 한번 보고 Detail하게 보는건 구현하면서  | 3월 까지 | 매 chapter끝날 때 마다 포인트 쌓게 하기  | 포인트 다 쌓으면 보상  |
| 1 | LPDDR5 CMD Parser구현 | CMD Parser가 기본 | CMD State Diagram이용해서 구현 | 3월 2째 주 주말까지 | parser다 구현하기 | 포인트 5점 추가  |
| 2 | LPDDR5 CMD AC Parameter checker구현 | AC Param Study를 위해서는 필수 | AC Parameter들 정리 후 구현 | 4월 2째 주까지  |  다 구현 | 포인트 10점 추가 |
| 3 | LPDDR5 Spec violation 구현 | Spec violation check를 위해선 필수 | Spec violation나는 것들 정리 | 5월2째주 까지 | 다 구현 | 포인트 10 점 추가 |
| 4 | DDR5 Spec Study  | Spec을 알아야 Program만들지  | Spec먼저 훅 한번 보고 Detail하게 보는건 구현하면서  |  | 매 chapter끝날 때 마다 포인트 쌓게 하기  | 포인트 다 쌓으면 보상  |
| 5 | DDR5 CMD Parser구현 | CMD Parser가 기본 | CMD State Diagram이용해서 구현 |  | parser다 구현하기 | 포인트 5점 추가  |
| 6 | DDR5 CMD AC Parameter checker구현 | AC Param Study를 위해서는 필수 | AC Parameter들 정리 후 구현 |   |  다 구현 | 포인트 10점 추가 |
| 7 | DDR5 Spec violation 구현 | Spec violation check를 위해선 필수 | Spec violation나는 것들 정리 |  | | |

### Programming
- what
  - 새로운 언어 2가지 제대로 배우기 (파이썬 / 자바)
  - 새로운 영역에 대해서 도전해 보기 (AI /Embedded OS)
  - 프로그래밍 기본에 대한 공부(SW engineering/문서 편집기 1개/Git/CI 제대로 공부)
- why
  - 새로운 언어를 알지 못하면, 도태될 수 밖에 없다. 
  - 개발자는 새로운 툴 및 도구에 항상 관심을 가져야 한다.
- how
  - 언어는 기초를 안 다음 고급 단계로 넘어가기 위해서는 실제로 사용하는 방법 밖에 없다. 
  - CMD Parser나 새로 짜는 것들은 Python과 JAVA를 이용한다.
  - 그리고 그 것들을 사용하는 툴로 새로운 문서편집기를 이용하고
  - 그리고 version관리하는 법을 제대로 이용하기 위해서 책을 읽는다.
  - CI관련해서도 책을 읽도록 한다.
  - 즉, 실제로 사용하지 않으면 절대로 늘지 않는다.
  - 따라서 Spec공부하면서 언어도 준비하도록 하자.

### Target
- New language : python, JAVA
- New Area : Embedded OS, Network 
- New Text Editor : Visual code
- New Version Manager : BitBucket 사용
- New TIDD method : python - UnitTest module 존재/ JAVA - JUnit 
- New Integration method : CI (Bamboo?)
- New SW Engineering method Study

#### Programming Study Table
| Index | Study 항목 | 왜 해야 하는가  | 어떻게 할 것 | 일정 | FeedBack | 보상 |
| ---| --- | --- | --- | --- | --- | --- |
| 1. | Python | AI 및 새로운 언어 제대로 학습 | Parser Program을 Python으로 짤 것 | Parser짜는 동안 계속 사용 | 일단 기본 공부 하고(자료형)  | Parser완성 시, 포인트 10점 |
| 2. | JAVA | 기본적인 Programming언어 습득 | Network관련 code작성 시, JAVA사용 | Network관련 공부 시 사용 | 고민!?  | 코드 완성도를 보고 결정  |
| 3. | Embedded OS | OS 한번 만들어 보는게 소원 | OS책 보면서 차근 차근 만들 것  | 9월 까지인데, 어떻게 할지는 고민 | chapter별로 어떻게 할지 정하자 | chapter별 Point할당  |
| 4. | Network | 기본 소양이지! network는! | 아직 미정..OS공부하면서 같이 할건데..  | 9월까지 같이 하자 | TBD  |   |
| 5. | Visual Studio Code Editor | 새로운 Text 편집기 사용 | 올해는 이것으로 사용하면서, 매일 Function key 2개씩 보자 | Function key또는 기능 3개 마다 점수 |   | 3개마다 0.2점  |
| 6 | bitbucket | 새로운 형상관리 tool | 올해 사용하는 모든 code bit bucket으로 관리 | 올해 계속 사용 | 새로운 기능 1개씩 찾아보기 | 올해 마지막에 판단 |
| 7 | Unit test tool | JAVA -JUnit/ Python UnitTest module사용 | Unit test는 안전한 코드의 기본이다. | 계속 사용 | 모든 코드의 Test code삽입 | 완성도에 따라 판단  |
| 8 | CI Study | CI How todo? trend | bit bucket과 Bamboo의 사용 | ?  | ?  | ?  |

### Photo
- what
- why
- how

### Target

#### Photo Study Table
| Index | Study 항목 | 왜 해야 하는가  | 어떻게 할 것 | 일정 | FeedBack | 보상 |
| ---| --- | --- | --- | --- | --- | --- |
|  |   |   |   |   |   |   |
|  |   |   |   |   |   |   |
|  |   |   |   |   |   |   |

### Excercise
- what
- why
- how

### Target

#### Excercise Study Table
| Index | Study 항목 | 왜 해야 하는가  | 어떻게 할 것 | 일정 | FeedBack | 보상 |
| ---| --- | --- | --- | --- | --- | --- |
|  |   |   |   |   |   |   |
|  |   |   |   |   |   |   |
|  |   |   |   |   |   |   |

### BigData & AI
- what
- why
- how

### Target

#### BigData & AI Study Table
| Index | Study 항목 | 왜 해야 하는가  | 어떻게 할 것 | 일정 | FeedBack | 보상 |
| ---| --- | --- | --- | --- | --- | --- |
|  |   |   |   |   |   |   |
|  |   |   |   |   |   |   |
|  |   |   |   |   |   |   |

# FeedBack Loop
-


# Point 적립 방법과 보상
-

# DailyStudyReport

-
-
-
-
