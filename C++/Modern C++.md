Modern C++ : C++11 이후의 C++

### auto
```cpp
#include <iostream>
#include <vector>
#include <map>
using namespace std;

// auto

class Knight
{
public:
	int hp;
};

template<typename T>
void Print1(T t)
{
	cout << t << "\n";
}

int main()
{
	int a = 3;
	float b = 3.2f;
	double c = 1.23;
	Knight* d = new Knight();
	const char* e = "asdg";
	// 위와 같이 다양한 타입을 잘 맞춰 정의해줘야함
	
	// 하지만 auto를 쓰면 간단하게 가능
	auto a = 3;
	auto b = 3.2f;
	auto c = 1.23;
	auto d = new Knight();
	auto e = "asdg";
	// 변수에 마우스를 오버해보면 각 타입을 알려줌

	// 그러면 어떻게 이걸 맞춰서 해줬을까? -> 그냥 초기화해준 데이터를 보고 그거의 헝태에 맞춰줌
	// template<typename T> 와 유사함.
	// 형식 연역, 추론 (type deduction)
	// 추론 규칙은 생각보다 복잡해질 수 있다.

	// 추론은 상식선에서 이루어짐.
	auto f = &d;
	const auto test1 = b;
	auto* test2 = e;

	// 주의점 : 기본 auto는 const, &를 무시한다.
	int& ref = a;
	const int cs = a;
	// 둘 다 그냥 int로 추론함
	auto test3 = ref;
	auto test4 = cs; 

	// 아래와 같은 실수 조심
	vector<int> v;
	v.push_back(1);
	v.push_back(1);
	v.push_back(1);
	for (int i = 0; i < v.size(); i++)
	{
		//int& data = v[i];
		auto data = v[i]; // 이렇게 하면 원본 데이터는 안 바뀜
		data = 100;
	}

	// 보통 언제 쓰냐? 타이핑이 너무 길어지는 경우
	map<int, int> m;
	auto a = m.insert(make_pair(1, 2));
	for (vector<int>::iterator it = v.begin(); it != v.end(); it++);

	// 그러면 무조건 auto만 사용하는게 좋나? 그건 아님
	// 가독성을 위해 일반적으로는 타입을 적어주는게 좋음.
}
```

### 중괄호 초기화
```cpp
#include <iostream>
#include <vector>
#include <map>
using namespace std;

// 중괄호 초기화

class Knight
{
public:
	Knight() 
	{

	}
	Knight(initializer_list<int> list)
	{
		cout << "Knight Initializer list\n";
	}
};

int main()
{
	int a = 1;
	int b{ 3 }; // 이렇게 초기화

	Knight k1;
	Knight k2{ k1 };

	vector<int> v(10, 2);

	int arr[] = { 1, 2, 3 };

	// 각 타입마다 초기화 방법이 조금씩 다른게 걸려서 이 방법이 생김
	// vector등 container와 잘 어울린다.
	vector<int> v2{ 1,2,3 };

	// 축소 변환 방지(검수가 깐깐해짐)
	int c = 5;
	double d{ c }; // 축소 변환이 필요함

	// bonus
	Knight k3{ }; // 기본 생성자로 생성
	Knight k4{ 1,2 }; // Initialize list 생성자와, 인자 2개인 생성자가 있으면?
	// Initialize list가 최상위로 제일 먼저 호출됨
	// 이런 경우 잘 생각하고 만들어야 함



	// 괄호 초기화 ()를 기본으로 간다.
	// - 전통적인 C++
	// - vector 등 특이한 케이스에 대해서만 { } 사용

	// 중괄호 초기화 { }를 기본으로 간다.
	// - 초기화 문법의 일치화
	// - 축소 변환 방지
}
```
