### 함수 템플릿
```cpp
#include <iostream>
using namespace std;

// 템플릿 기초

//void Print(int a)
//{
//	cout << a << "\n";
//}

// 여러가지 타입으로 변수를 받고싶으면?
// 오버로딩하면 됨
// 그런데 엄청 종류가 많다면?
// 이 부분을 템플릿을 이용해서 구현 가능

class Knight
{
public:
	//
public:
	int hp = 100;
};

template<typename T> // typename 대신에 class를 넣어도 됨.
void Print(T a)
{
	cout << a << "\n";
}

// 인자가 여러개를 커버하려면?
template<typename T1, typename T2>
void Print(T1 a, T2 b)
{
	cout << a << " " << b << "\n";
}

template<typename T>
T Add(T a, T b)
{
	return a + b;
}

// 연산자 오버로딩 (전역함수)
ostream& operator<<(ostream& os, const Knight& k)
{
	os << k.hp;
	return os;
}

// 템플릿 특수화
// 특정 타입에 대해서만 다른 규칙을 적용하고 싶을 때
template<>
void Print(Knight a)
{
	cout << "~Knight~\n";
	cout << a.hp << "\n";
}

int main()
{
	// 템플릿 : 함수나 클래스를 찍어내는 틀
	// 함수 템플릿
	// 클래스 템플릿

	Print(50);
	Print("Hello");
	Print(1.42f);

	Print<int>(13); // 이렇게 하면 명시적으로 int버전만 사용할 수 있음

	int result = Add(1, 2);

	Print("adsd", 3);

	Knight k1;
	Print(k1); // 출력안됨. 그래서 보통 <<를 오버로딩 하긴함
}
```

