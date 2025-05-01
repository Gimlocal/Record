### 함수 기초
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

### 지역 변수와 값 전달
```cpp
#include <iostream>
using namespace std;

// 지역 변수와 값 전달

// 전역 변수 (메모리에서 데이터 영역)
// 함수 내부든 외부든 어디서든 사용가능
int globalValue = 0;

void IncreaseHp(int hp)
{
	hp = hp + 1;
}

int main()
{
	// 지역 변수 (메모리에서 스택 영역)
	// 그 함수 내에서만 사용가능
	int localValue = 1;

	int hp = 1;
	cout << hp << "\n";
	IncreaseHp(hp);	// 2가 안됨. 1이 나옴.
	cout << hp << "\n";

	// 스택 프레임 구조
	// [매개변수][RET][지역변수][매개변수][RET][지역변수][매개변수][RET][지역변수]
	// 메인에서의 hp는 지역변수에 저장되는데, IncreaseHp의 hp는 매개변수 부분에 저장돼서 같은 hp가 아니게 됨.
	// 사실상 IncreaseHp(1)과 같은 작용을 함.


	return 0;
}
```

### 호출 스택
```cpp
#include <iostream>
using namespace std;

// 호출 스택
// 디버깅 시 어떤 경로로 이 함수가 실행되었는지 볼 수 있음.
// 스택 메모리의 반환 주소값을 하나하나 다 적어준것과 비슷

// 함수 선언을 먼저 해준다.
void Func1();
void Func2(int, int);
void Func3(float);

void Func1()
{
	cout << "Func1\n";

	Func2(1, 2);
}
// Func1을 훑을 때, Func2는 정의되지 않았으므로 오류 발생.

void Func2(int a, int b)
{
	cout << "Func2\n";

	Func3(10);
}

void Func3(float a)
{
	cout << "Func3\n";
}

int main()
{
	cout << "main\n";
	Func1();
}
```


