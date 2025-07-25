### 동적 할당
```cpp
#include <iostream>
using namespace std;

// 동적 할당

// 메모리 구조 복습
// - 실행할 코드가 저장되는 영역 -> 코드 영역
// - 전역(global)/정적(static) 변수 -> 데이터 영역
// - 지역 변수/매개 변수 -> 스택 영역
// - 동적 할당 -> 힙 영역

// 지금까지 데이터 영역 / 스택 영역 이용했는데 굳이 새로운 영역이 필요할까

// ex. 
// MMORPG 동접 1명~5만명 -> 몬스터 1마리 ~ 500만마리
// 몬스터 생성 이벤트 -> 5분동안 몬스터가 10배 더 나옴 -> 5천만마리 생성? 

// - 스택 영역 
// -- 함수가 끝나면 같이 정리되는 불안정한 메모리
// -- 잠시 함수에 매개변수 넘긴다거나, 하는 용도로는 ok
// 
// - 메모리 영역
// -- 프로그램이 실행되는 도중에는 무조건 사용되는
//
// 필요할때만 사용하고, 필요없으면 반납할 수 있는 그런 메모리
// 그러면서도 스택과는 다르게 우리가 생성/소멸 시점을 관리할 수 있는 메모리
// - 힙 메모리(Heap)
// -- 동적 할당과 연관된 함수/연산자 : malloc, free, new, delete, new[], delete[] 
// 
// malloc
// - 할당할 메모리 크기를 건내준다.
// - 메모리 할당 후 시작 주소를 가리키는 포인터를 반환해준다. (메모리 부족하면 NULL 뜸)

// free (malloc과 짝)
// - malloc (혹은 기타 calloc, realloc 등)을 통해 할당한 영역을 해제
// - 힙 관리자가 할당/미할당 여부를 구분해서 관리

// new / delete
// - C++에서 추가됨
// - malloc/free는 함수 -> new/delete는 연산자

// new[] / delete[]
// - new가 malloc에 비해 좋긴 한데 배열과 같이 N개 데이터를 같이 할당하려면?

// malloc/free vs new/delete
// - 사용 편의성 -> new/delete
// - 타입에 상관없이 특정한 크기의 메모리 영역을 할당받으려면? -> malloc/free

// 그런데 둘의 가장 큰 차이는?
// new/delete는 (생성타입이 클래스일 경우) 생성자/소멸자를 호출해준다! (명시적으로 생성/소멸자를 만들어줘야 함)


class Monster
{
public:
	Monster() { cout << "Monster\n"; }
	~Monster() { cout << "~Monster\n"; }
public:
	int hp;
	int x;
	int y;
};

//Monster monster[500 * 10000]; // 유동적으로 몬스터 수를 늘리거나 줄일 수 없음.

int main()
{
	// 컴퓨터
	// 유저 영역 [메모장] [게임] [플레이어] 등의 프로그램 (각 프로그램은 서로를 모르게 독립적으로 돌아감)
	// 메모리를 써야할 경우 커널 영역에 접근해 허락을 받아 메모리를 사용
	// ------------------------
	// 커널 영역 (Windows 등의 핵심 코드)
	// 
	// 동적 할당 순서
	// 유저 영역) 운영체제에서 제공하는 API 호출
	// 커널 영역) 메모리 할당해서 건내줌
	// 유저 영역) 메모리 사용
	// 
	// 그런데 이렇게 필요할 때마다 계속 요청하나?
	// 기본적으로 어느정도의 크기의 메모리를 주고 더 필요하면 가져가 쓰는 형식
	// 
	// C++에서는 기본적으로 CRT(C런타임 라이브러리)의 힙 관리자를 통해 힙 영역 사용
	// 단, 정말 필요하다면 직접 API를 통해 힙을 생성하고 관리할 수도 있음(서버 메모리 풀링)
	// 

	
	// 그런데 void* 는 뭘까 -> *가 있으니까 주소를 담는 바구니
	// 그런데 주소를 타고가면 void? 아무것도 없나? no
	// 뭐가 있는지 모르니까 적당히 변환해서 사용해라 ok
	void* pointer = malloc(sizeof(Monster)); // 1000바이트 만큼 영역을 할당해 pointer에 주소를 넣음

	Monster* m1 = (Monster*)pointer;
	m1->hp = 100;
	m1->x = 1;
	m1->y = 1;
	// Heap Overflow - 유효한 힙 범위를 초과해서 사용하는 문제

	free(pointer); // 크기를 안 넣어도 되는 이유는 malloc을 할 때 크기또한 메모리에 넣어두기 때문
	// 즉 12바이트만 할당하는게 아니라 이것과 관련된 메모리를 조금 더 만듬.
	// 그런데 할당하고 free를 안해주면? -> 보이지는 않지만 메모리 누수가 생김 -> 언젠가는 프로그램이 뻗음
	// 거꾸로 free를 여러번 하면? -> 크래시가 남.
	// 가장 안좋은 상황 : User-Ater-Free
	// pointer를 free했지만 아직 주소가 남아있기 때문에 접근이 가능함.
	// 이걸 건드리면 다른 메모리를 건드리는게 될 수 있음.
	pointer = nullptr;
	m1 = nullptr; // 이렇게 널로 만들어줌.


	Monster* m2 = new Monster; // 한 마리 생성
	m2->hp = 100;
	m2->x = 1;
	m2->y = 1;
	delete m2;

	Monster* m3 = new Monster[5]; // 여러 마리 생성(잘 안씀)
	m3->hp = 100;
	m3->x = 1;
	m3->y = 1;

	Monster* m4 = (m3 + 1);
	m4->hp = 100;
	m4->x = 1;
	m4->y = 1;

	delete[] m3;

	return 0;
}
```

### 타입 변환
```cpp
#include <iostream>
using namespace std;

// 타입 변환

// malloc -> void* 를 반환하고, 이를 우리가 타입 변환을 통해 사용했었음.
// 

class Knight
{
public:
	int hp = 10;
};

class Dog
{
public:
	Dog() { }
	// 타입 변환 생성자 (이걸 만드는 순간 기본 생성자를 만들어줘야함)
	Dog(const Knight& knight)
	{
		age = knight.hp;
	}

	// 타입 변환 연산자
	operator Knight()
	{
		return (Knight)(*this);
	}
public:
	int age = 1;
	int cuteness = 2;
};

class BullDog : public Dog
{
public:
	bool french;
};

int main()
{
	// --- 타입 변환 유형 (비트열 재구성 여부)
	// 1. 값 타입 변환
	// - 의미를 유지하기 위해서, 원본 객체와 다른 비트열 재구성
	{
		int a = 123456789; // 2의 보수
		float b = (float)a; // 부동소수점(지수 + 유효숫자) (그대로 안 나옴)
		cout << b << "\n";
	}

	// 2. 참조 타입 변환
	// - 비트열을 재구성하지 않고, 관점만 바꾸는 것
	// 거의 쓸 일은 없지만, 포인터 타입 변환도 참조 타입 변환과 동일한 룰을 따르기 때문에 일석이조
	{
		int a = 123456789; // 2의 보수
		float b = (float&)a; 
		cout << b << "\n";
	}


	// --- 안전도 분류
	// 1. 안전한 변환
	// - 의미가 항상 100% 완전히 일치하는 경우
	// 같은 타입이면서 크기만 더 큰 바구니로 이동
	// 작은 바구니 -> 큰 바구니로 이동 OK (업캐스팅)
	// ex) char -> short, short -> int, int -> __int64
	{
		int a = 123456789;
		__int64 b = a;
		cout << b << "\n";
	}
	
	// 2. 불안전한 변환
	// - 의미가 항상 100& 일치한다고 보장하지 못하는 경우
	// 타입이 가르거나 큰 바구니 -> 작은 바구니 이동 (다운캐스팅)
	{
		int a = 123456789;
		float b = a;
		short c = a;
		cout << c << "\n";
	}


	// --- 의도에 따라 분류
	// 1. 암시적 변환
	// - 이미 알려진 타입 변환 규칙에 따라서 컴파일러가 자동으로 타입 변환
	{
		int a = 123456789;
		float b = a; // 암시적으로 float으로 변환 (값이 같지는 않음)
		// 물론 모든 타입으로 암시적 변환을 해주지는 않음
		cout << b << "\n";
	}

	// 2. 명시적 변환
	{
		int a = 123456789;
		int* b = (int*)a; // 명시적으로 변환
		cout << b << "\n";
	}

	
	// --- 아무런 연관 관계가 없는 클래스 사이의 변화
	// 1. 연관없는 클래스 사이의 값 타입 변환
	// 일반적으로 안됨 -> 예외적으로 타입 변환 생성자 연산자가 있으면 가능
	{
		Knight knight;
		Dog dog = (Dog)knight; // 타입 변환 생성자
		Knight knight2 = dog; // 타입 변환 연산자
	}

	// 2. 연관없는 클래스 사이의 참조 타입 변환
	{
		Knight knight;
		// 어셈블리 입장에서는 포인터 = 참조
		// [ 주소 ] -> [ Dog ] 이 형식으로 만들어줌
		// 마치 void* 처럼 가서 뭐가 있는지 모르는 느낌
		Dog& dog = (Dog&)knight;
		dog.cuteness = 12; // 이상한 메모리를 건들 수 있음
	}


	// --- 상속 관계에 있는 클래스 사이의 변환
	// 자식 -> 부모 ok, 부모 -> 자식 no
	// 1. 상속 관계 클래스의 값 타입 변환
	{
		/*Dog dog;
		BullDog bullDog = dog;*/ // 이건 안됨

		BullDog bulldog;
		Dog dog = bulldog; // 이건 됨
	}

	// 2. 상속 관계 클래스의 참조 타입 변환
	{
		Dog dog;
		BullDog& bulldog = (BullDog&)dog; // 명시적 필요함

		BullDog bullDog;
		Dog& dog = bullDog; // 얘는 암시적으로 Dog의 내용을 다 들고있기 때문에 암시적으로 가능
	}


	// 결론
	// 값 타입 변환 : 진짜 비트열도 바꾸고, 논리적으로 말이 되게 바꾸는 볍ㄴ환
	// - 논리적으로 말이 된다 : Bulldog -> Dog (ok)

	// 참조 타입 변환 : 비트열은 놔두고 우리의 관점만 바꾸는 변환
	// - 명시적 변환을 하면 해주긴 하는데, 말 안 해도 그냥 암시적으로 해주는지는 안정성 여부와 연관이 있음.
	// - 메모리 침범 위험이 있는 경우는 그냥 암시적으로 해주진 않음.

}
```

### 타입 변환 (포인터)
```cpp
#include <iostream>
using namespace std;

// 타입 변환 (포인터)

class Knight
{
	int hp = 0;
};

class Item
{
public:
	Item()
	{
		cout << "Item()" << "\n";
	}

	Item(int itemType) : itemType(itemType)
	{
		cout << "Item(int itemType)" << "\n";
	}

	Item(const Item& item)
	{
		cout << "Item(const item&)" << "\n";
	}

	virtual ~Item()
	{
		cout << "~Item()" << "\n";
	}
public:
	int itemType;
	int itemDbId;

	char dummy[4096] = {}; // 이런저런 정보들로 인해 비대해진 정보 배열
};

enum ItemType
{
	WEAPON = 1,
	ARMOR = 2,
};

class Weapon : public Item
{
public:
	Weapon() : Item(WEAPON)
	{
		cout << "Weapon()\n"; 
		damage = rand() % 100;
	}
	~Weapon() { cout << "~Weapon()\n"; }
public:
	int damage = 1;
};

class Armor : public Item
{
public:
	Armor() : Item(ARMOR)
	{ 
		cout << "Armor()\n"; 
	}
	~Armor() { cout << "~Armor()\n"; }
public:
	int defence = 1;
};

void TestItem(Item item)
{

}

void TestItemPtr(Item* item)
{

}

int main()
{
	// 복습
	{
		// Stack 메모리에 올라감 [ type(4) dbid(4) dummy(4096) ]
		Item item; // 이 친구는 소멸자를 호출함

		// Stack [ 주소(4~8) ] -> Heap [ type(4) dbid(4) dummy(4096) ]
		Item* item2 = new Item(); // 소멸자를 호출 안함

		TestItem(item);
		TestItem(*item2); // 이렇게 복사 생성자를 하면 사이즈가 큰 놈이 마구 생성되기 때문에 조심해야함.

		TestItemPtr(&item);
		TestItemPtr(item2);


		delete item2; // new / delete 안하면 메모리 누수 일어남 (Memory Leak) -> 가용 메모리가 점점 줄어듬
	}

	// 배열
	{
		cout << "---------------------------------\n";

		Item item3[100] = {}; // 진짜 아이템이 100개 있는 것 (100개)

		cout << "---------------------------------\n";

		Item* item4[100] = {}; // 아이템을 가리키는 바구니가 100개 (아이템이 없을 수 있음)

		for (int i = 0; i < 100; i++)
			item4[i] = new Item(); // 아이템 넣어주기

		for (int i = 0; i < 100; i++)
			delete item4[i];

		cout << "---------------------------------\n";
	}


	// 연관성이 없는 클래스 사이의 포인터 변환 테스트
	{
		// Stack [ 주소 ] -> Heap [ hp(4) ]
		// 4바이트만 차지하고 있음
		Knight* knight = new Knight;
		// Stack [ 주소 ]
		// Item의 크기는 엄청큰데 knight로 item을 만들었기 때문에 첫번째 변수인 itemType (4바이트)는 문제없이 변환가능하지만
		// 그 다음부터는 문제가 발생함. 문제가 일어나는 지도 모르고 메모리가 오염됨
		Item* item = (Item*)knight; // 암시적으로는 안되지만 명시적으로는 안됨
		//item->itemType = 2;
		//item->itemDbId = 1; 메모리 오염 가능

		delete knight; // 이 부분이서 컴파일러가 잡아주긴 함
		// 타입변환을 잘 해야함 
	}

	// 자식 -> 부모 변환 테스트
	{
		Weapon* weapon = new Weapon();

		Item* item = weapon; // 무기라면 무조건 아이템이기 때문에 안전해서 암시적으로도 가능

		delete weapon;
	}

	// 부모 -> 자식 변환 테스트
	{
		Item* item = new Item();

		Weapon* weapon = (Weapon*)item; // 아이템은 무기라는 보장이 없음
		// 즉 메모리가 작은거에서 큰거로 가는것이기 때문에 그냥은 막힘
		// 물론 명시적으로는 가능함. 하지만 잘못된 메모리를 건들 수 있음

		delete item;
	}

	// 명시적으로 타입 변환할 때는 항상 조심해야 한다.
	// 그러면 암시적으로 될 때는 안전한가?
	// -> 평생 명시적으로 타입 변환은 안 하면 되는거 아닌가?
	
	Item* inventory[20] = {};
	srand((unsigned int)time(nullptr));
	for (int i = 0; i < 20; i++)
	{
		int randValue = rand() % 2; // 0-1
		switch (randValue)
		{
		case 0:
			inventory[i] = new Weapon;
			break;
		case 1:
			inventory[i] = new Armor();
			break;
		}
	}

	for (int i = 0; i < 20; i++)
	{
		Item* item = inventory[i];
		if (item == nullptr)
			continue;
		if (item->itemType == WEAPON)
		{
			Weapon* weapon = (Weapon*)item; // 원래 weapon이었기 때문에 이런 변환이 괜찮긴함
			cout << "Weapon Damage : " << weapon->damage << "\n";
		}
	}

	// 결국 타입 변환 시에는 항상 조심해야함.

	// *******매우 중요********
	for (int i = 0; i < 20; i++)
	{
		Item* item = inventory[i];
		if (item == nullptr)
			continue;

		// delete item; // 이렇게 하면 어떻게 되나
		// item의 소멸자만 호출이됨.
		// 하지만 inventory에는 Weapon과 Armor이 있기 때문에 이것들의 소멸자가 호출되어야함
		// 그렇기 때문에 아래와 같이 잘 delete해주는게 올바름
		//if (item->itemType == WEAPON)
		//{
		//	Weapon* weapon = (Weapon*)item;
		//	delete weapon;
		//}
		//else if (item->itemType == ARMOR)
		//{
		//	Armor* armor = (Armor*)item;
		//	delete armor;
		//}

		// 그런데 왜 각각의 소멸자가 알아서 잘 호출되지 않나?
		// 그냥 부른 변수가 Item이기 때문에. 이걸 위해서 이전에 virtual 함수를 써서 동적으로 변경하기도 했음
		// 소멸자도 똑같이 적용하게됨. 그렇기 때문에 최상위 클래스의 소멸자에는 virtual을 붙이는 편임
		// 그래서 이제는 그냥 item을 delete해도됨
		delete item;
	}

	// 결론
	// - 포인터 vs 일반 타입 : 차이를 이해해야함
	// - 포인터 사이의 타입 변환(캐스팅)을 할 때는 매우 조심해야 한다.
	// - 부모자식 관계에서 부모 클래스의 소멸자에는 까먹지 말고 virtual을 붙이자! (중요)
}
```

### 얕은 복사 vs 깊은 복사
```cpp
#include <iostream>
using namespace std;

// 얕은 복사 vs 깊은 복사

class Pet
{
public:
	Pet()
	{
		cout << "Pet()\n";
	}
	~Pet()
	{
		cout << "~Pet()\n";
	}
	Pet(const Pet& pet)
	{
		cout << "Pet(const Pet&)\n";
	}
};

class Knight
{
public:
	Knight()
	{
		pet = new Pet();
	}
	Knight(const Knight& knight)
	{
		hp = knight.hp;
		//pet = knight.pet; // 이 형식은 얕은 복사
		pet = new Pet(*knight.pet); // 이렇게 깊은 복사로 별개의 pet을 만들어줘야함 (깊은 복사)
	}
	Knight& operator=(const Knight& knight) 
	{
		hp = knight.hp;
		pet = new Pet(*knight.pet); // 마찬가지로 깊은 복사
		return *this;
	}
	~Knight()
	{
		delete pet;
	}
public:
	int hp = 100;
	Pet* pet;
};

int main()
{
	Pet* pet = new Pet();
	Knight knight; // 기본 생성자
	knight.hp = 200;
	knight.pet = pet; // 이렇게 하면 knight를 복사했을 때 모두 같은 Pet을 공유하게 됨


	Knight k2 = knight; // 복사 생성자
	Knight k3(knight); 
	Knight k4; // 기본 생성자
	k4 = knight; // 이후 복사 대입 연산자

	// 복사 생성자, 복사 대입 연산자
	// 둘 다 안 만들어주면 컴파일러가 암시적으로 만들어줌

	// 중간 결론 -> 컴파일러가 잘 만들어준다? 굳이 뭘 해야하나?


	// ---얕은 복사(Shallow Copy)---
	// 멤버 데이터를 비트열 단위로 똑같이 복사 (메모리 영역 값을 그대로 복사)
	// Stack : Knight1 [ hp, 0x1000(pet) ] -> Heap 0x1000 Pet [ ] 
	// Stack : Knight2 [ hp, 0x1000(pet) ] -> Heap 0x1000 Pet [ ] 
	// Pet이 동일한 객체를 가리키게 됨.
	
	// 하지만 Knight의 생성자에 Pet을 만들고, 소멸자에 delete하는 코드를 추가하면 프로그램이 망가짐
	// Knight2~4가 소멸이 되면서 pet을 소멸시키는데, 모두 같은 pet을 가리키고 있으니까 같은 pet을 4번 소멸하는 오류가 발생하기 때문

	// 결국 문제는 얕은 복사를 했다는 것.
	// 우리가 원하는건 깊은 복사임
	

	// ---깊은 복사(Deep Copy)---
	// 멤버 데이터가 참조(주소) 값이라면, 데이터를 새로 만들어준다. (원본 객체가 참조하는 대상까지 새로 만들어서 복사)
	// 포인터는 주소값 바구니 -> 새로운 객체를 생성 -> 상이한 객체를 가리키는 상태가 됨.
	// Stack : Knight1 [ hp, 0x1000(pet) ] -> Heap 0x1000 Pet [ ] 
	// Stack : Knight2 [ hp, 0x2000(pet) ] -> Heap 0x2000 Pet [ ] 
	// Stack : Knight3 [ hp, 0x3000(pet) ] -> Heap 0x3000 Pet [ ] 
	// 위와같이 해주는 것

	// 실험
	// - 암시적 복사 생성자 Steps
	// -- 1. 부모 클래스의 복사 생성자 호출
	// -- 2. 멤버 클래스의 복사 생성자 호출
	// -- 3. 멤버가 기본 타입일 경우 메모리 복사 (얕은 복사)
	// - 명시적 복사 생성자 Steps
	// -- 1. 부모 클래스의 기본 생성자 호출
	// -- 2. 멤버 클래스의 기본 생성자 호출
	// -- 이렇게 때문에 부모나 멤버 클래스의 복사 생성자는 따로 초기화 리스트 방법으로 해줘야함

	// - 암시적 복사 대입 연산자 Steps
	// -- 1. 부모 클래스의 복사 대입 연산자 호출
	// -- 2. 멤버 클래스의 복사 대입 연산자 호출
	// -- 3. 멤버가 기본 타입일 경우 메모리 복사 (얕은 복사)
	// - 명시적 복사 대입 연산자 Steps
	// -- 알아서 해주는거 없음
	// -- 이 부분도 연산자 오버로딩 시 함수 내에 pet이나 Player의 복사 대입 연산자에 대한 코드의 추가가 필요함

	// 왜 이렇게 복잡하게 되어있을까?
	// 객체를 복사한다는 것은 두 객체의 값들을 일치시키려는 것
	// 따라서 기본적으로 얕은 복사 방식으로 동작
	// 명시적 복사가 들어가는 순간 모든 책임은 프로그래머가 안음
}
```

### 캐스팅 4가지
```cpp
#include <iostream>
using namespace std;

// 캐스팅 4가지

// 1. static_cast
// 2. dynamic_cast
// 3. const_cast
// 4. reinterpret_cast

class Player
{
public:
	virtual ~Player() {}
};

class Knight : public Player
{

};

class Archer : public Player
{

};

class Dog
{

};

void PrintName(char* str)
{
	cout << str << "\n";
}


int main()
{
	// static_cast : 타입 원칙에 비춰볼 때 상식적인 캐스팅만 허용
	// 1. int <-> float 
	// 2. Player* -> Knight* (다운캐스팅) << 단 안정성은 보장 못함

	int hp = 100;
	int maxHp = 200;
	float ratio = static_cast<float>(hp) / maxHp; // 0.5가 아닌 0이 나옴. 그래서 캐스팅

	Player* p = new Archer();
	Knight* k = static_cast<Knight*>(p); // 위험한 코드임. Player를 Archer로 만들었으면? 메모리가 잘못될 수 있음


	// dynamic_cast : 상속 관계에서의 안전한 형변환
	// RTTI (RunTIme Type Information)
	// 다형성을 활용하는 방식
	// virtual 함수를 하나라도 만들면, 객체의 메모리에 가상 함수 테이블 주소가 기입된다.
	// 만약 잘못된 타입으로 캐스팅을 했으면, nullptr을 반환
	Knight* k2 = dynamic_cast<Knight*>(p); // 현재 p를 Archer로 만들었으므로, Knight로 변환이 안됨. 그래서 nullptr로 반환

	// 맞는 타입으로 캐스팅을 했는지 확인을 하는데 유용하다.
	// 하지만 이런 과정때문에 static_cast보다는 살짝 느리다


	// const_cast : const를 붙이거나 떼거나 할 때 사용
	PrintName(const_cast<char*>("asd")); // "asd"는 const 변수이기 때문에 매개변수로 못 들어감. 이때 const_cast를 붙임


	// reinterpret_cast
	// 가장 위험하고 강력한 형태의 캐스팅
	// : 포인터랑 전혀 관계없는 다른 타입 변환 등

	__int64 address = reinterpret_cast<__int64>(k2);

	Dog* dog = reinterpret_cast<Dog*>(k2);

	void* ptr = malloc(1000);
	Dog* d2 = reinterpret_cast<Dog*>(ptr);
}
```
