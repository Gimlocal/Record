### 함수 포인터1
```cpp
#include <iostream>
using namespace std;

// 함수 포인터

int Add(int a, int b)
{
	return a + b;
}

int Sub(int a, int b)
{
	return a - b;
}

class Item
{
public:
	Item() : itemId(0), rarity(0), ownerId(0)
	{

	}
public:
	int itemId;
	int rarity;
	int ownerId;
};


// 함수를 인자로 넣어줄 수 도 있음.
// 하지만 인자의 개수를 정확히 맞춰줘야함.
typedef bool(ITEM_SELECTOR)(Item* item);
Item* FindItem(Item items[], int itemCount, ITEM_SELECTOR* selector)
{
	for (int i = 0; i < itemCount; i++)
	{
		Item* item = &items[i];
		if (selector(item))
			return item;
	}

	return nullptr;
}

bool IsRareItem(Item* item)
{
	return item->rarity >= 2;
}

int main()
{
	int a = 10;

	// 포인터 : *
	// 변수이름 : ptr
	// 데이터 타입 : int
	int* ptr = &a;

	// 함수 
	typedef int(FUNC_TYPE)(int a, int b); // int 두개를 받아서 int 하나를 뱉는 함수
	
	// 포인터 : *
	// 변수이름 : fn
	// 데이터 타입 : 함수(인자는 int 2개, 반환은 int 1개)
	FUNC_TYPE* fn;

	// 함수 포인터
	// 여러 함수를 한번에 고칠 수 있다.
	fn = Sub;

	// 함수의 이름은 함수의 시작 주소 (배열과 유사)
	int result = fn(1, 2);
	cout << result << "\n";

	int result = (*fn)(1, 2); // 함수 포인터는 접근 연산자(*)가 붙어도 함수 주소임
	cout << result << "\n";

	Item items[10] = {};
	items[3].rarity = 2;

	Item* rareItem = FindItem(items, 10, IsRareItem);

	return 0;
}
```

### 함수 포인터 2
```cpp
#include <iostream>
using namespace std;

// 함수 포인터

// typedef 왼쪽 오른값 -> 오른쪽 (커스텀 타입 정의)
typedef int NUMBER;

// 정확히는 왼쪽/오른쪽 기준이 아니라.
// [선언 문법]에서 typedef를 앞에다 붙이는 느낌

int number7;
typedef int number;

typedef int FUNC();

typedef int (*PFUNC)(int, int); // 일반 함수 포인터

class Knight
{
public:
	static void HelloKnight()
	{

	}

	int GetHp(int, int)
	{
		cout << "GetHp()\n";
		return hp;
	}
public:
	int hp = 100;
};

typedef int(Knight::*PMEMFUNC)(int, int); // 멤버 함수 포인터

int Test(int , int)
{
	cout << "Test\n";
	return 0;
}



int main()
{
	PFUNC fn1;
	fn1 = &Test;

	typedef int(FUNC_TYPE)(int, int);
	FUNC_TYPE* fn = &Test;
	fn(1, 1);

	// 위 문법으로 만들면 전역 함수 / 정적 함수 만 담을 수 있음. (규약이 동일한 함수들)
	//fn = &Knight::GetHp; 이건 안됨

	Test(1, 2);
	
	PMEMFUNC mfn;
	mfn = &Knight::GetHp; // 멤버 함수는 &를 붙여줘야함.

	Knight k1;
	(k1.*mfn)(1, 1);

	Knight* k2 = new Knight();
	(k2->*mfn)(1, 1);

	delete k2;

	return 0;
}
```

