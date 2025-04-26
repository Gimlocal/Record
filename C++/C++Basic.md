#### 정수
```cpp
#include <iostream>
using namespace std;

int hp = 100;	// 초기값이 있으면 .data 영역 없으면 .bss 영역

char a;		// 1바이트 (-128 ~ 127)
short b;	// 2바이트
int c;		// 4바이트
__int64 d;	// 8바이트(long long)

unsigned char ua;	// 1바이트 (0 ~ 255)
unsigned short ub;	// 2바이트
unsigned int uc;		// 4바이트
unsigned __int64 ud;	// 8바이트(long long)
// unsigned가 붙으면 음수가 아닌 양수만 표현

int main()
{
	b = 32767;
	b += 1;
	cout << b << "\n";  // 0111~1111에서 1000~0000이 됐기 때문에 음수로 바뀜
	
	ub = 0;
	ub -= 1;
	cout << ub;   // 1000~0000에서 0111~1111이 됐기 때문에 양수로 바뀜
}
```

#### 불리안과 실수
```cpp
#include <iostream>
using namespace std;

// 불리언과 상수
bool flag = true;

// bool은 그냥 1바이트 정수
// 어셈블리에서 bool이라는 것은 없음
// 단지 1이냐 아니냐의 차이

// 실수 (부동소수점)
// 3.14 ...

float speed = -3.375f;		// 4바이트 (f를 붙여야함)
double speed2 = 1332.4123;	// 8바이트

// 3.14를 표현.
// 점 앞/뒤를 기준으로 16/16씩 끊으면?
// (0~65535) . (0~65535) 까지밖에 표현 못함

// 부동소수점 (.을 유동적으로 움직여서 표현하는 방법)
// 3.1415926535
//	1. 정규화 = 0.31415926535 * 10
//	2. 31415926535 (유효숫자) 1 (지수) 로 표현함

// float 부호(1) 지수(8) 유효숫자(23) = 32비트 = 4바이트
// double 부호(1) 지수 (11) 유효숫자(52) = 64비트 = 8바이트


// ex) -3.375라는 값을 저장
// 1. 2진수로 변환
// 1-1 0.375 = 0.5 * 0 + 0.25 * 1 + 0.125 * 1 = 0b0.011
//	3.375 = 3 + 0.375 = 0b11 + 0b0.011 = 0b11.011
// 2. 정규화 -> 0b11.011 > 0b1.1011 * 2^1
//	1(부호) 1(지수) 1011(유효숫자)
//  단 지수는 unsigned byte라고 가정하고 숫자 +127 만들어줌
// 예상 결과 : 0b 1 10000000 1011000 0000 0000 0000 0000

// 부동소수점은 항상 근사값임을 기억.
// 특히 수가 커질수록 오차범위도 커짐.
// 그래서 실수끼리의 비교도 지양해야함.


int main()
{
	cout << flag;
}
```

#### 문자와 문자열
```cpp
#include <iostream>

using namespace std;

// 문자와 문자열
// bool은 그냥 정수지만, 참/거짓을 나타내기 위해 사용되었음
// 사실 char도 마찬가지. 그냥 정수지만 '문자' 의미를 나타내기 위해 사용

// char : 알파벳 / 숫자 문자를 나타낸다
// wchar_t : 유니코드 문자를 나타낸다.

// ASCII (American Standart Code for Information Interchange)

// a b c d e 1 2 3 4 -> 사람의 문자

// '문자'의 의미로 작은 따옴표 '' 사용
char ch = 'a';
char ch2 = '1';
char ch3 = 'a' + 1;

// 영어만으로 서비스 할 수는 없음
// 전 세계 모든 문자에 대해 유일한 코드를 부여한 것이 유니코드 (unicode)
// 유니코드에서 가장 많은 번호를 차지하는게 한국어/중국어

// 유티코드는 표기 방식이 여러가지가 있는데 대표적으로 UTF8 UTF16
// UTF8
// - 알파벳, 숫자 1바이트(ASCII 동일한 번호)
// - 유럽 지역의 문자는 2바이트
// - 한글, 한자 등 3바이트
// UTF16
// - 거의 대부분 문자 2바이트
// - 아주 예외적인 고대 문자만 4바이트(사실상 무시해도됨)

wchar_t wch = L'안';

// Escape Sequence
// 표기하기 애매한 문자들을 표현
// \0 = NULL
// \t = Tab
// \n = LineFeed
// \r = Carriage (커서를 처음으로)
//	엔터키는 \n + \r
// \' = 작은 따옴표


// 문자열
// 문자들이 열을 지어서 모여 있는 것

// 끝은 NULL (0)

char str[] = { 'h', 'e', 'l', 'l', 'o' , '\0' };
char str2[] = "Hello World";	// 자동으로 마지막에 NULL을 추가해줌
wchar_t str3[] = L"Hello World";

int main()
{
	//cout << ch << " " << ch2;
	//cout << "\n" << ch3 << "\n";
	//// cout은 char 전용

	//wcout.imbue(locale("kor"));	// 한글은 이렇게 해야 출력가능
	//wcout << wch << "\n";

	//char str[] = { 'h', 'e', 'l', 'l', 'o' , '\0' };	// 스택에서 사용하면 C스타일로 마지막에 NULL을 붙여줘야함
	cout << str << "\n";
	cout << str2 << "\n";
	wcout << str3 << "\n";
}
```

#### 데이터 연산
```cpp
#include <iostream>
using namespace std;

// 데이터 연산
// 데이터를 가공하는 방법에 대해

int a = 1;	// a라는 이름의 바구니를 할당하고 안에 1을 넣는다.
int b = 2;

// 산술 연산자
// 대입 연산
// 사칙 연산



int main()
{
#pragma region 산술연산
	// 대입 연산
	// a = b
	// b라는 바구니 안에 있는 값을 a라는 바구니 안에 복사하고 b를 반환
	// a = b = 3 -> b = 3, a = b와 동일
	a = b;

	// 사칙 연산
	// a = b + 3;
	// 뺄셈, 곱셈, 나눗셈, 나머지 등등(-, *, /, %)
	// a = a + 1 -> a += 1과 동일
	a = b + 3;
	a = b - 3;
	a = b * 3;
	a = b / 3;
	a = b % 3;

	// 증감 연산자
	// a++, a--, ++a, --a 
#pragma endregion
}
```

