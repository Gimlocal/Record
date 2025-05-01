### 정수
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

### 불리안과 실수
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

### 문자와 문자열
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

### 데이터 연산
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

### 비교 연산 & 논리 연산
```cpp
#include <iostream>
using namespace std;

// 데이터 연산
// 데이터를 가공하는 방법에 대해

int a = 1;	// a라는 이름의 바구니를 할당하고 안에 1을 넣는다.
int b = 2;

bool isSame;
bool isDifferent;
bool isGreater;
bool isSmaller;

bool test;

int hp = 100;
bool isInvincible = true;

int main()
{
#pragma region 비교 연산
	// 언제 필요한가?
	// ex) 체력이 0이되면 사망
	// ex) 체력이 일정 이하일 때 궁극기 발동 등등..

	// a == b : a와 b의 값이 같은가?
	// 같으면 1, 다르면 0
	isSame = (a == b);

	// a != b : a와 b의 값이 다른가?
	// 다르면 1, 같으면 0
	isDifferent = (a != b);

	// a > b, a >= b : a와 b의 값의 크기비교
	// 맞으면 1, 아니면 0
	isGreater = (a > b);

	// a < b, a <= b
	// 맞으면 1, 아니면 0
	isSmaller = (a < b);
#pragma endregion

#pragma region 논리 연산
	// 언제 필요한가? 조건에 대한 논리적 사고가 필요할 때
	// ex) 로그인할 때, 아이디도 같고 AND 비밀번호도 같아야 한다.
	// ex) 길드 마스터이거나 OR 운영자 계정이면 길드 해산이 가능.

	// ! 조건
	// 틀리면 1, 맞으면 0
	test = !isSame; // 사실상 isDifferent의 의미

	// && (and)
	// a && b : 둘다 맞으면 1, 그 외는 0
	test = (hp <= 0 && isInvincible == false); // 사망

	// || (or)
	// a || b : 둘중 하나라도 맞으면 1, 둘다 틀리면 0
	test = (hp > 0 || isInvincible == true); // 생존
	// test = !(hp <= 0 && isInvincible == false)와 동일
#pragma endregion
}
```

### 비트 연산
```cpp
#include <iostream>
using namespace std;

// 데이터 연산
// 데이터를 가공하는 방법에 대해

unsigned char flag; // 부호를 없애서 >>에서 오류 방지

int main()
{
#pragma region 비트 연산
	// 언제 필요한가? (사실 많이는 안 쓰임)
	// 비트 단위의 조작이 필요할 때
	// - 대표적으로 BitFlag
	
	// ~ : bitwise not
	// 단일 숫자의 모든 비트를 대상으로, 0은 1 1은 0으로 변환

	// & : bitwise and
	// 두 숫자의 모든 비트 쌍을 대상으로 and 연산을 한다.

	// | : bitwise or
	// 두 숫자의 모든 비트 쌍을 대상으로 or 연산을 한다.

	// ^ : bitwise xor
	// 두 숫자의 모든 비트 쌍을 대상으로 xor 연산을 한다.

	// << : 비트 좌측 이동
	// 비트열을 N만큼 왼쪽으로 이동한다.
	// 왼쪽의 넘치는 비트는 버리고, 오른쪽에 새로운 부분은 0으로 채워진다.
	// *2를 할 때 자주 보이는 패턴 (왼쪽으로 한칸 이동)

	// >> : 비트 우측 이동
	// 비트열을 N만큼 오른쪽으로 이동한다.
	// 오른쪽의 넘치는 비트는 버림
	// 왼쪽의 새로운 비트는
	// - 부호 비트가 존재할 경우 부호 비트를 따라감 (부호 있는 정수라면 이 부분을 유의)
	// - 아니면 0

	// 실습
	// 0b0000 [무적][변이][스턴][공중부양]
	// 4개의 비트로 위의 4가지 상태를 표현

	// 무적 상태로 만든다
	flag = (1 << 3);

	// 변이 상태를 추가한다 (무적 + 변이)
	flag |= (1 << 2);

	// 무적인지 확인하고 싶다? (다른 상태는 관심 x)
	// bitmask
	bool invincible = (flag & (1 << 3)) != 0;

	// 무적이거나 스턴 상태인지 확인하고 싶다면?
	bool mask = (1 << 3) | (1 << 2);
	bool stunOrInvincible = ((flag & mask) != 0);
#pragma endregion
}
```

### Const와 메모리 구조
```cpp
#include <iostream>
using namespace std;

// const와 메모리 구조
// 한번 정해지면 절대 바뀌지 않을 값들
// constant의 약자인 const를 붙임 (변수를 상수화)
// const를 붙이면 초기값을 지정해줘야함

unsigned char flag;

//int AIR = 0;
//int STUN = 1;
//int POLYMORPH = 2;
//int INVINCIBLE = 3;
// 이렇게하면 안전하지 않음


// 그러면 const도 바뀌지 않는 읽기 전용? .rodata?
// 컴파일러 마음. 표준에서 그렇게 하라는 말은 없음
const int AIR = 0;
const int STUN = 1;
const int POLYMORPH = 2;
const int INVINCIBLE = 3;


// 전역 변수 (main밖에서 변수 지정)

// [데이터 영역] (함수 외부)
// .data (초기값 존재)
int a = 2;
// .bss (초기값 존재x)
int b;
// .rodata (읽기 전용 데이터)
const char* msg = "Hello World";

int main()
{
	// [스택 영역] : [데이터 영역]도 사용가능 (함수 내부)
	int c = 3;

#pragma region 비트 연산
	// 무적 상태로 만든다
	flag = (1 << INVINCIBLE);

	// 변이 상태를 추가한다 (무적 + 변이)
	flag |= (1 << POLYMORPH);

	// 무적인지 확인하고 싶다? (다른 상태는 관심 x)
	// bitmask
	bool invincible = (flag & (1 << INVINCIBLE)) != 0;

	// 무적이거나 스턴 상태인지 확인하고 싶다면?
	bool mask = (1 << INVINCIBLE) | (1 << POLYMORPH);
	bool stunOrInvincible = ((flag & mask) != 0);
#pragma endregion
}
```

### 유의사항
```cpp
#include <iostream>
using namespace std;

// 유의사항, 팁

// 1. 변수의 유효범위

//	전역 변수
//	int hp = 10; 전역일 때는 상관 x
//	스택에 들어가있는 변수일 때는 함수 내부에서만 사용 가능

//	한 함수에서 같은 변수를 여러번 지정하려면 중괄호를 한번 더 하면 가능하긴함
//	전역 변수 지역 변수가 겹치면 지역 변수가 쓰임


// 2. 연산 우선순위

//	우선순위가 많고 복잡하기에 외우기 보다는 애매할 때는 괄호를 이용한다.


// 3. 타입 변환
//	바구니 교체
int hp = 77777;
short hp2 = (short)hp;
float hp3 = (float)hp;	// 이런 방식으로 가능 (자동으로 해주기도 함)
//	큰 값을 작은 값으로 바꿀때는 조심해야함. 줄어든 바이트만큼 값이 차이날 수 있음.
//	실수로 변환할 때 정밀도 차이가 있기 때문에 데이터 손실 가능
//	부호의 유무에 따라 값이 바뀌기도 함 int의 -1은 unsigned int의 FFFF~FFFF


// 4. 사칙 연산
//	곱셈 - 오버플로우 조심. int * int = int이지만 값이 커지면 오버플로우 가능
//	나눗셈 - 0으로 나누는 실수 조심, 실수 관련 (int / int = int. float이 안됨)

int main()
{
	
}
```
