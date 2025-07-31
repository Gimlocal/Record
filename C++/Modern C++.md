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
