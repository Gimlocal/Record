### 함수 템플릿
```cpp
#include <iostream>
using namespace std;

// 함수 템플릿

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

### 클래스 템플릿
```cpp
#include <iostream>
using namespace std;

// 클래스 템플릿

// 만약 멤버 안에서 타입을 변경할 일이 있으면?
// 단, 이 경우 명시적으로 타입을 지정해줘야함
// 그런데 무조건 typenamea만 붙여야 하는건 아님
// 매개변수 처럼 더 넣을 수 있음.
// 단, 이 경우 만약 SIZE값이 다른 클래스를 만들면 아예 다른 클래스로 인식됨.
template<typename T, int SIZE>
class Random
{
public:
	T GetRandomData()
	{
		int idx = rand() % SIZE;
		return data[idx];
	}
public:
	T data[SIZE];
};

// 템플릿 특수화
template<int SIZE>
class Random<double, SIZE> // 클래스 이름 옆에 이렇게 추가적으로 정보를 적어줘야함.
{
public:
	double GetRandomData()
	{
		int idx = rand() % SIZE;
		cout << "Random Double\n";
		return data[idx];
	}
public:
	double data[SIZE];
};


int main()
{
	srand(static_cast<unsigned int>(time(nullptr)));
	// 템플릿 : 함수나 클래스를 찍어내는 틀
	// 함수 템플릿
	// 클래스 템플릿

	Random<int, 10> r1; // 명시적 지정
	for (int i = 0; i < 10; i++)
	{
		r1.data[i] = i;
	}
	int val1 = r1.GetRandomData();
	cout << val1 << "\n";

	Random<double, 20> r2;
	for (int i = 0; i < 20; i++)
	{
		r2.data[i] = i + 0.5;
	}
	double val2 = r2.GetRandomData();
	cout << val2 << "\n";
}
```

### 콜백 함수
```cpp
#include <iostream>
using namespace std;

// 콜백 (Callback)

class Item
{
public:

public:
	int itmeId = 0;
	int rarity = 0;
	int ownerId;
};

class FindByOwnerId
{
public:
	bool operator()(const Item* item)
	{
		return (item->ownerId == ownerId);
	}
public:
	int ownerId;
};

class FindByRarity
{
public:
	bool operator()(const Item* item)
	{
		return (item->rarity == rarity);
	}
public:
	int rarity;
};

template<typename T>
Item* FindItem(Item items[], int itemCount, T selector)
{
	for (int i = 0; i < itemCount; i++)
	{
		Item* item = &items[i];
		if (selector(item))
			return item;
	}
	return nullptr;
}

int main()
{
	// 함수 포인터 + 함수 객체 + 템플릿
	// 콜백 : 다시 호출하다

	// 게임을 만들 때 이런 콜백의 개념이 자주 등장한다.
	// ex. MoveTask 등

	// 어떤 상황이 일어나면 -> 이 기능을 호출해줘.
	// ex. 어떤 UI 스킬 버튼을 누르면 -> 스킬을 쓰는 함수를 호출

	Item items[10];
	items[3].ownerId = 100;
	items[8].rarity = 1;

	FindByOwnerId func1;
	func1.ownerId = 100;

	FindByRarity func2;
	func2.rarity = 1;

	Item* item1 = FindItem(items, 10, func1);
	Item* item2 = FindItem(items, 10, func2);
}
```
