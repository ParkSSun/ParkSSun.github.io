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

- 위의 예제들을 통해서 알수있는 점은 WBCS를 위한 함수들이 따로 존재한다는 것이다.
- Main함수의 경우에도 함수인자로 SBCS을 받도록 되어 있다.
- WBCS을 위한 Main함수는 다음 아래와 같이 wmain이고 input인자로 wchar_t*([])을 받도록 되어있다.

```c++
typedef unsigned short wchart_t;
int main(int argc, char* argv[]){

}

int wmain(int argc, wchar_t* argv[]){

}
```
# MBCS와 WBCS의 동시 지원
---
- MBCS를 지원할 것인가 WBCS를 지원 할 것인가 하는 문제는 따로 코딩을 해야하는 문제가 발생한다.
- 따라서 이를 해결 하기 위한 방법에 대해서 알아보자.
- **시스템 프로그래밍시 이제 아래에서 제안하는 방법으로 코딩을 하도록 하자.**

## #include <windows.h>
- windows.h는 윈도우즈 프로그래밍을 하면서 기본적으로 항상 포함하는 header파일이다.
- 이 헤더파일 내에는 CHAR와 WCHAR가 정의 되어 있다. 각각 MBCS와 WBCS를 나타내는 자료형이다.

## windows에서 정의하고 있는 자료형
- 많은 자료형들이 windows 시스템 프로그래밍에 맞게 새로 정의되어 있지만, 몇 가지만 제안하도록 하겠다.
```c++
typedef char CHAR;
typedef wchar_t WCHAR;
#define CONST const
typedef CHAR* LPSTR;
typedef CONST CHAR* LPCSTR;
typedef WCHAR* LPWSTR;
typedef CONST WCHAR* LPCWSTR;

int wmain(int argc, LPSTR argv[]){
    LPSTR str1 = "SBCS style String";
	LPWSTR str2 = L"WBCS Style String";
	CHAR str3[] = "SBCS style String1";
	WCHAR str4[] = L"WBCS Style String1";
	LPCSTR str5 = str1;
	LPCWSTR str6 = str2;
	printf("%s\n", str1);
	printf("%s\n", str3);
	wprintf(L"%s\n", str2);
	wprintf(L"%s\n", str4);
	return;
}
```
- 위에 코드에서 확인할 수 있듯이, 새로운 자료형이 재정의되었는데, 이는 절대적인 것은 아니다. 프로젝트를 진행하는 사람들 끼리 서로 약속한다면 새로운 자료형을 정의해서 사용할 수 있다.

## MBCS와 WBCS를 동시에 지원하기 위한 매크로.
