#### 함수 기초
```cpp
#include <iostream>
using namespace std;

// 함수 (프로시저, 메소드, 루틴 등등)

/* 양식
	input으로 무엇을 받고, output으로 무엇을 받을지 정해준다.
	
	반환타입 함수이름(매개변수(input))
	{
		함수 내용
		return (output)
	}
*/

// Hello World를 콘솔에 출력하는 함수 만들기
// input 없음, output 없음
// 타입 : int, float, double, char ....
// 반환(output) 타입이 없으면 void
void PrintHelloWorld()
{
	cout << "Hello World\n";
}

// 정수를 입력 받아서, 콘솔에 출력하는 함수 만들기
void PrintNumber(int a)
{
	cout << a << "임\n";
}

// 2를 곱하는 함수 만들기
int Double(int a)
{
	return 2 * a;
}

// 두 숫자를 곱해서 결과물을 주는 함수 만들기
int Multiple(int a, int b)
{
	return a * b;
}

int main()
{
	PrintHelloWorld();
	PrintNumber(5);
	cout << Double(3) << "\n";
	cout << Multiple(5, 8) << "\n";
}
```


