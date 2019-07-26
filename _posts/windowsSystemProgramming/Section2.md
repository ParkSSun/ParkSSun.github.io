---
title: "System programming - section 2. 아스키 코드 VS 유니코드"
date: 2019-07-26 00:13:00
categories: WindowsSystemProgramming
---
# Windows에서의 유니코드
---
## 문자셋의 종류와 특성
- 대표적 character set으로는 ASCII Code, UNICode가 있다.
- ASCII Code
    -  1byte로 문자 및 확장문자들을 표현 (256개 표현 가능) :  이는 미국에서의 표준
- UNICode
    - 2byte로 문자 및 확장문자들을 표현(65,536개 표현 가능) : 우리나라 및 다른 나라에서 사용.
- **문자셋이란 문자들이 집합, 약속된 문자의 표현방법을 의미**
### character set의 종류
- SBCS(Single Byte Character Set)
    - **문자를 표현하는데 있어서 1byte만을 사용하는 것을 SBCS이라 한다.**
    - 대표적인 SBCS는 ASCII Code이다.
- MBCS(Multi Byte Character Set)
    - **문자를 표현하는데 있어서 1byte 또는 2byte를 사용한다.**
    - 즉 2byte로 처리해야 할 문자는 2byte로 처리하고, 2byte로 처리해야 할 문자는 1byte로 처리한다.
    - 그럼 엄청 메모리 효율적으로 쓰네?!
        - 아니다.프로그래밍을 하는 사람 입장에서는 사용하기 힘들다.
        - 다음 아래 코드를 통해서 문제를 확인해 보자.
        - 코드의 결과 값은 size : 8, len : 7이다.
        - 프로그래머는 문자열의 길이를 5라고 생각할 것이다.
        - 7인 이유는 (영어는 1, 한글은2 로 문자열의 길이가 계산되고 있다. by MBCS)
```c++
int main(){
	char str[] = "ABC한글";
	int size = sizeof(str);
	int len = strlen(str);

	printf("배열의 크기 : %d\n", size);
	printf("문자열 길이 : %d\n", len);
	return 0;
}
```

- WBCS(Wide Byte character Set)
    - **문자열을 표현하는 것을 모두 2byte로 처리하는 것이다.**
    - 유니코드가 대표적인 WBCS이다.
    - 위의 MBCS에서 발생한 문제를 해결하는 방법을 다음 아래의 코드를 통해서 확인해 보자.
    - 다음 아래 코드의 결과는 size : 8, len : 5이다.
    - 실제로 프로그래머 입장에서는 MBCS을 이용해서 코딩할때 실수를 할 수 있지만, WBCS으로 한다면 2byte로 모두 통일 되므로 헷갈리지 않고 코딩을 할 수 있다는 장점이 있다.

```c++

int main(){
	wchar_t str[] = L"ABC한글";
	int size = sizeof(str);
	int len = wcslen(str);
	printf("배열의 크기 : %d\n", size);
	printf("문자열 길이 : %d\n", len);
	return 0;
}
```
