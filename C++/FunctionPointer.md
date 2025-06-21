### 함수 포인터
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
