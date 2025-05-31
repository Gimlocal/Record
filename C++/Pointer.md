### 포인터1
```cpp
#include <iostream>
using namespace std;

// 포인터

int main()
{
	int number = 1;

	// TYPE* 변수이름;
	// 2가지 요소
	// - TYPE
	// *

	// 바구니는 바구니인데
	// [주소]를 저장하는 바구니이다.
	// 변수를 선언할 때 * -> 포인터 = 주소
	// 현재 x64(8바이트)이므로 8바이트 고정 크기
	int* ptr = &number;

	// 그래서 주소로 뭘 하는거지?
	// 추가 문법 : [주소를 저장하는 바구니]기 가리키는 주소로 가서 뭐를 해라
	// *변수이름 = 값;

	// 변수 선언 시 * : 주소를 저장하는 바구니 (포인터 정의)
	// 변수 사용할 때 : 그 주소의 값에 접근

	int value1 = *ptr;
	*ptr = 2;

	return 0;
}
```

### 포인터2
```cpp
#include <iostream>
using namespace std;

// 포인터

void SetHp(int* hp)	// 주소를 받게 변경.
{
	*hp = 100;
}

int main()
{
	int number = 1;
	// TYPE은 왜 붙여줄까?
	// * : 포인터, 주소를 저장하는 바구니 = 4 or 8바이트 고정 크기
	// 고정인데 왜 int같이 타입을 붙여줄까?
	// 추가 정보를 줌
	// 주소에 가면 뭐가 있는가? 에 대해 정보를 줌.

	//int* ptr2; // ptr2 주소를 타고 가면 int값이 있다. 라는 정보를 줌.

	// 타입이 불일치한다면?
	_int64* ptr2 = (_int64*) & number;

	*ptr2 = 0xAABBCCDDEEFF;	// 원래 number의 영역보다 큰 영역을 덮어씌움. 데이터 손실 가능.

	int hp;
	//SetHp(hp);	// 이렇게 해도 main의 hp는 안변함.
	// 그런데 원본을 바꾸고 싶으면?
	SetHp(&hp);

	return 0;
}
```

### 포인터 연산
```cpp
#include <iostream>
using namespace std;

// 포인터 연산

// 1 주소 연산자 (&)
// 2 산술 연산자 (+, -)
// 3 간접 연산자 (*)
// 4 간접 멤버 연산자 (->)

struct Player
{
	int hp = 100;
	int damage = 10;
};

int main()
{
	int number = 1;
	// 1 주소 연산자 (&)
	// - 해당 변수의 주소를 알려줘
	// - 정확히는 해당 변수 타입에 따라서 TYPE*을 반환
	int* pointer = &number;

	// 2 산술 연산자 (+, -)
	
	number += 1;
	/*number = number + 1;
	number++;
	number += number;*/ // 등과같은 연산을 포인터에도 사용가능

	pointer += 1;	// 실제로는 4가 증가함 ?
	// 분석해보면 8바이트 주소를 담는 바구니이다.
	// int : 주소를 따라가면 4바이트 정수형 바구니가 있다.

	// 즉 1을 더하는것의 의미는 단순히 1을 더하는게 아니라
	// 바구니 크기만큼 이동을 한다는거다. 즉 4바이트를 증가시켜서
	// 다음 바구니로 이동한다는 말임.
	// 그래서 선언 TYPE에 따라 다르게 움직임

	// 3 간접 연산자 (*)
	number = 3;
	*pointer = 3;		// 두 연산이 동일함

	// 4 간접 멤버 연산자 (->)
	Player player;
	Player* playerPtr = &player;

	(*playerPtr).hp = 200;
	(*playerPtr).damage = 20;	// 방식이 귀찮음

	playerPtr->hp = 200;
	playerPtr->damage = 20;		// 간접 연산자가 좀 더 간편함.
	// 원리는 동일함.
}
```

### 포인터 실습
```cpp
#include <iostream>
using namespace std;

// 포인터 실습

struct StatInfo
{
	int hp;			// +0
	int attack;		// +4
	int defence;	// +8
};

void EnterLobby();
StatInfo CreatePlayer();
void CreateMonster(StatInfo* info);
bool StartBattle(StatInfo* player, StatInfo* monster); // 승리 여부

int main()
{
	EnterLobby();

	return 0;
}

void EnterLobby()
{
	cout << "로비에 입장 ㅎㅇ\n";

	StatInfo player;
	player.hp = 0xbbbbbbbb;
	player.attack = 0xbbbbbbbb;
	player.defence = 0xbbbbbbbb;

	// [매개변수][RET][지역변수(temp(100, 10, 2), player(100, 10, 2))][매개변수(&temp)][RET][지역변수(ret(100, 10, 2))]
	// 과정이 조금 있음. temp라는 임시 변수(struct)를 만들어서 그 변수를 매개변수로 넘겨주고,
	//  그곳에 100, 10, 2를 넣고(ret) 다시 돌아와서 그 변수를 player로 복사함.
	player = CreatePlayer();


	StatInfo monster;
	monster.hp = 0xbbbbbbbb;
	monster.attack = 0xbbbbbbbb;
	monster.defence = 0xbbbbbbbb;

	// temp없음. 복잡한 복사 과정이 줄어듬
	// [매개변수][RET][지역변수(monster(b, b, b))][매개변수(&monster)][RET][지역변수(ret(100, 10, 2))]
	// 매개변수로 받은 주소값에 바로 변수값들을 넣어줌
	CreateMonster(&monster);
	
	// 구조체끼리 복사할 때 무슨일이 벌어지나.
	// player = monster;
	// 바로 그냥 복사가 되는게 아닌 각각의 변수들을 한번씩 넣어주는 것.
	// player.hp = monster.hp ......

	bool victory = StartBattle(&player, &monster);

	if (victory) cout << "승리!\n";
	else cout << "패배!\n";
}

StatInfo CreatePlayer()
{
	StatInfo ret;
	
	cout << "플레어이 생성\n";
	
	ret.hp = 100;
	ret.attack = 10;
	ret.defence = 2;

	return ret;
}

void CreateMonster(StatInfo* info)
{
	cout << "몬스터 생성\n";

	info->hp = 40;
	info->attack = 8;
	info->defence = 1;
}

bool StartBattle(StatInfo* player, StatInfo* monster)
{
	while (true)
	{
		// 플레이어 공격
		int damage = player->attack - monster->defence;
		if (damage < 0)
			damage = 0;

		monster->hp -= damage;
		if (monster->hp < 0)
			monster->hp = 0;

		cout << "몬스터 HP : " << monster->hp << "\n";

		if (monster->hp == 0)
			return true;

		// 몬스터 공격
		damage = monster->attack - player->defence;
		if (damage < 0)
			damage = 0;

		player->hp -= damage;
		if (player->hp < 0)
			player->hp = 0;

		cout << "플레이어 HP : " << player->hp << "\n";

		if (player->hp == 0)
			return false;
	}
}
```

### 참조 기초
```cpp
#include <iostream>
using namespace std;

// 참조

struct StatInfo
{
	int hp;
	int attack;
	int defence;
};

// [매개변수][RET][지역변수(info)] [매개변수(&info)][RET][지역변수]
void CreateMonster(StatInfo* info)
{
	info->hp = 100;
	info->attack = 8;
	info->defence = 5;
}

// [매개변수][RET][지역변수(info)] [매개변수(info)][RET][지역변수]
void CreateMonster(StatInfo info)
{
	info.hp = 100;
	info.attack = 8;
	info.defence = 5;
}

// 값을 수정하지 않는다면, 양쪽 다 문제 없음

// 1. 값 전달 방식
// [매개변수][RET][지역변수(info)] [매개변수(info)][RET][지역변수]
// info 복사가 일어나서 그 복사된 info를 사용함
void PrintInfoByCopy(StatInfo info)
{
	cout << "-------------------------" << "\n";
	cout << "HP : " << info.hp << "\n";
	cout << "Attack : " << info.attack << "\n";
	cout << "Defence : " << info.defence << "\n";
	cout << "-------------------------" << "\n";
}

// 2. 주소 전달 방식
// [매개변수][RET][지역변수(info)] [매개변수(&info)][RET][지역변수]
// 복사가 아니라 원본의 주소를 통해 접근해 원본을 사용
void PrintInfoByPtr(StatInfo* info)
{
	cout << "-------------------------" << "\n";
	cout << "HP : " << info->hp << "\n";
	cout << "Attack : " << info->attack << "\n";
	cout << "Defence : " << info->defence << "\n";
	cout << "-------------------------" << "\n";
}

// 현재는 Struct가 크기가 작아서 크게 상관없어 보이지만, 
// 만약에 엄청 커진다면 복사가 일어날때마다 메모리 차이가 많이 날 수 있음.
// 한 가지 방법이 더 있음

// 3. 참조 전달 방식
// 값 전달처럼 편리하고 주소 전달처럼 진짜를 건드릴 수 있다.
void PrintInfoByRef(StatInfo& info)
{
	cout << "-------------------------" << "\n";
	cout << "HP : " << info.hp << "\n";
	cout << "Attack : " << info.attack << "\n";
	cout << "Defence : " << info.defence << "\n";
	cout << "-------------------------" << "\n";
}

int main()
{
	// 4바이트 정수형 바구니와 그 주소를 담는 바구니와 연산
	int number = 1;
	int* pointer = &number;
	*pointer = 2;

	// 로우레벨(어셈블리) 관점에서 실제 작동 방식은 int*와 동일
	int& reference = number;
	// C++ 관점에서는 number라는 바구니에 또 다른 이름을 부여한 것.
	// number라는 바구니에 reference라는 다른 이름을 지어줌.
	reference = 3; // 이렇게 하면 number에 3이 넣어짐

	// 그런데 또 다른 이름을 짓는 이유는? 포인터를 쓰면 되지않나?
	// 참조 전달 때문.


	StatInfo info;
	CreateMonster(&info);

	PrintInfoByCopy(info);
	PrintInfoByPtr(&info);
	PrintInfoByRef(info);

	return 0;
}
```

### 포인터 vs 참조
```cpp
#include <iostream>
using namespace std;

// 포인터 vs 참조
// 성능 : 똑같음
// 편의성 : 참조가 조금 더 편함

// 1. 편의성 관련
// 편의성이 좋다는게 꼭 장점은 아님.
// 포인터는 주소를 넘기니 확실하게 원본을 넘긴다는 것을 알 수 있음.
// 참조는 자연스럽게 모르고 넘길 수 있음.
// 마음대로 고친다면? -> const를 이용해서 막을 수 있음.

// 포인터에서도 const 사용가능 * 기준으로 앞, 뒤가 다름.
// 포인터 [주소값] 주소값[데이터]
// * 앞에 붙이면 : 데이터를 고치지 못함
// * 뒤에 붙이면 : 주소값을 고치지 못함.
// 즉 고치지 못하는 바구니의 내용물이 바뀜
// 물론 둘 다 붙일 수 있음.



// 2. 초기화 여부
// 참조 타임은 바구니의 2번째 이름(별칭)
// -> 참조하는 대상이 없으면 안됨.
// 즉 정의할 때 StatInfo& reference; 와 같이 정의 불가능(초기값 필수)

// 포인터는 초기값 없이 정의 가능(그냥 주소라는 의미이기 때문)
// 실존하지 않을 수도 있긴 때문에 Null(0)으로 해도 가능(nullptr)
// 어떠한 주소도 가르키지 않는 상태 : nullptr
// 그렇기 때문에 어떤 오브젝트를 찾는 상황에서 nullptr 반환이 가능한 포인터도 장점이 있다.
// 이때는 찾는 함수안에 예외처리(if nullptr~)를 해줘야함.


// 그래서 결론은?
// 상황에 따라 다름.
// 구글에서의 오픈소스를 보면 거의 무조건 포인터.
// 언리얼 엔진에선 reference도 애용

// 예외처리를 위해 포인터 사용에 이점.
// 바뀌지 않고 읽는 용도(readonly)는 참조에 이점.

// 일반적으로는? ref (바뀔 수 있으면 명시적으로 OUT붙이기)
// 보통 섞어 사용하지는 않음.


struct StatInfo
{
	int hp;
	int attack;
	int defence;
};

// [매개변수][RET][지역변수(info)] [매개변수(&info)][RET][지역변수]
void CreateMonster(StatInfo* info)
{
	info->hp = 100;
	info->attack = 8;
	info->defence = 5;
}

void PrintInfoByPtr(StatInfo* info)
{
	cout << "-------------------------" << "\n";
	cout << "HP : " << info->hp << "\n";
	cout << "Attack : " << info->attack << "\n";
	cout << "Defence : " << info->defence << "\n";
	cout << "-------------------------" << "\n";
}

void PrintInfoByRef(const StatInfo& info)
{
	cout << "-------------------------" << "\n";
	cout << "HP : " << info.hp << "\n";
	cout << "Attack : " << info.attack << "\n";
	cout << "Defence : " << info.defence << "\n";
	cout << "-------------------------" << "\n";
}

#define OUT
void ChangeInfo(OUT StatInfo& info)
{
	info.hp = 1000;
}

int main()
{
	StatInfo info;
	CreateMonster(&info);

	StatInfo* pointer = nullptr;
	pointer = &info;
	PrintInfoByPtr(&info);

	// StatInfo& reference; 안됨
	StatInfo& reference = info;
	PrintInfoByRef(reference);

	ChangeInfo(OUT info);
	// 주의해서 볼 수 있음. 가독성 측에서

	// Bonus 포인터 -> 참조 or 참조 -> 포인터로 넘겨주려면?
	PrintInfoByRef(*pointer);
	PrintInfoByPtr(&reference);

	return 0;
}
```

### 배열 기초
```cpp
#include <iostream>
using namespace std;

// 배열

struct StatInfo
{
	int hp = 0xAAAAAAAA;
	int attack = 0xBBBBBBBB;
	int defence = 0xDDDDDDDD;
};

int main()
{
	// TYPE 이름[개수]
	StatInfo monsters[10];

	// 배열의 크기는 상수여야 함
	const int monsterCount = 10;
	StatInfo monsterss[monsterCount];

	// 여태껏 변수들의 이름은 바구니의 이름이었음
	int a = 10;
	int b = a;

	// 그런데 배열은 이름이 다르게 동작함
	// monsters = monsterss 불가능

	// 그럼 배열의 이름은 뭐지?
	// 배열의 이름은 곧 배열의 시작 주소이다.
	// 정확히는 시작 위치를 가리키는 TYPE* 포인터
	// 그래서 monsters 자체로 주소임.
	auto WhoAmI = monsters;

	StatInfo* monster_0 = monsters;
	monster_0->hp = 100;
	monster_0->attack = 10;
	monster_0->defence = 1;

	StatInfo* monster_1 = monsters + 1;
	monster_1->hp = 200;
	monster_1->attack = 20;
	monster_1->defence = 2;

	StatInfo& monster_2 = *(monsters + 2); // 참조로도 가능
	monster_2.hp = 300;
	monster_2.attack = 30;
	monster_2.defence = 3;

	// 이거는 완전 다른 의미
	StatInfo temp = *(monsters + 2); // 그냥 복사본
	temp.hp = 400;
	temp.attack = 40;
	temp.defence = 4;

	// 조금 더 자동화 가능
	for (int i = 0; i < 10; i++)
	{
		StatInfo* monster = monsters + i;
		monster->hp = 100 * (i + 1);
		monster->attack = 10 * (i + 1);
		monster->defence = (i + 1);
	}

	// 하지만 포인터를 이용해서 하니까 가독성이 좀 떨어짐
	// 그래서 index를 이용해서 배열에 접근 가능
	// *(monsters + i) = monsters[i]

	monsters[0].hp = 100;
	monsters[0].attack = 10;
	monsters[0].defence = 1;

	// 배열 초기화 문법 몇가지
	int numbers[5] = { }; // 0으로 초기화
	int numbers1[10] = { 1,2,3,4,5 }; // 5개는 숫자가 들어가고 나머지는 0
	int numbers2[] = { 1,2,3,5,1245,163 }; // 데이터 개수만큼 크기 만들어줌

	return 0;
}
```

### 포인터 vs 배열
```cpp
#include <iostream>
using namespace std;

// 포인터 vs 배열


// 배열은 함수 인자로 넘기면, 컴파일러가 포인터로 치환한다.(char[] -> char*)
void Test(char a[])
{
	a[0] = 'x';
}

int main()
{
	// .rdata 주소[H][e][l][l][o][][W][o][r][l][d][\0] 
	// test1 [ 주소 ] << 8바이트 
	const char* test1 = "Hello World";

	// 4바이트씩 복사해서 3번의 과정을 거쳐 넣어줌
	// 별도의 바구니가 만들어진것이 아님
	char test2[] = "Hello World";

	// 포인터는 주소를 담는 바구니
	// 배열은 같은 데이터끼리 붙어있는 바구니 모음
	// 다만 [배열 이름]은 바구니 모음의 시작 주소

	Test(test2);
	cout << test2;	// 결과가 바뀜(포인터로 들어가서)

	return 0;
}
```

### 연습문제
```cpp
#include <iostream>
using namespace std;

// 연습문제

void Swap(int& a, int& b)
{
	int temp = a;
	a = b;
	b = temp;
}

void Sort(int numbers[], int count)
{
	for (int i = 0; i < count - 1; i++)
	{
		int best = i;

		for (int j = i + 1; j < count; j++)
		{
			if (numbers[j] < numbers[best]) best = j;
		}

		Swap(numbers[i], numbers[best]);
	}
}

void ChooseLotto(int numbers[], int count)
{
	srand((unsigned)time(0));
	for (int i = 0; i < count; i++)
	{
		while (true)
		{
			int num = rand() % 45 + 1;
			bool flag = true;
			for (int j = 0; j < i; j++)
			{
				if (numbers[j] == num) flag = false;
			}
			if (flag)
			{
				numbers[i] = num;
				break;
			}
		}
	}
	Sort(numbers, count);
}

int main()
{
	// 1. Swap 함수 만들기
	int a = 1;
	int b = 2;
	Swap(a, b); // a = 2, b = 1

	cout << a << " " << b << "\n";

	// 2. 정렬 함수 만들기
	int numbers[6] = { 1, 421, 13, 235, 16, 9 };
	// 6이 바뀌면? -> 배열의 크기를 넣어줘야함
	// sizeof(numbers) / sizeof(int) 하면 됨.
	Sort(numbers, sizeof(numbers) / sizeof(int)); // 오름차순으로 정렬하기
	for (int i = 0; i < sizeof(numbers) / sizeof(int); i++)
	{
		cout << numbers[i] << " ";
	}
	cout << "\n";

	// 3. 로또 번호 생성기
	int numbers2[6];
	ChooseLotto(numbers2, sizeof(numbers2) / sizeof(int));
	for (int i = 0; i < sizeof(numbers2) / sizeof(int); i++)
	{
		cout << numbers2[i] << " ";
	}
}
```

### 다중 포인터
```cpp
#include <iostream>
using namespace std;

// 다중 포인터

void SetMessage(const char* a)
{
	a = "Bye";
}

void SetMessage(const char** a) // 다중 포인터
{
	*a = "Bye";
}

void SetMessage2(const char*& a) // 참조도 동일하게 가능
{
	a = "Bye2";
}

int main()
{
	// .rdata 주소[H][e][l][l][o][\0]
	// msg[ 주소 ] << 8바이트
	const char* msg = "Hello";

	// [매개변수][RET][지역변수(msg(Hello주소))][매개변수(a(Hello주소))][RET][지역변수]
	// .rdata Bye주소[B][y][e][\0]으로 새로 만듬
	SetMessage(msg); // 안 바뀜
	//cout << msg << "\n";

	// pp[ msg주소 ] << 8바이트 (포인터)
	// msg주소[ 주소 ] << 8바이트
	// 주소 [ ] << 1바이트(char)
	const char** pp = &msg;
	// [매개변수][RET][지역변수(msg(Hello주소))][매개변수(a(msg주소))][RET][지역변수]
	SetMessage(&msg);
	cout << msg << "\n";
	SetMessage2(msg);
	cout << msg << "\n";
}
```

### 다차원 배열
```cpp
#include <iostream>
using namespace std;

// 다차원 배열

int main()
{
	int a[10] = { 1, 2, 3 };

	int first[5] = { 4, 2, 3, 4, 1 };
	int second[5] = { 1, 5, 2, 3, 1 };
	// ... 이렇게 계속 만들수는 없음
	// 2차원 배열 이용

	int apartment2D[2][5] = { { 4, 2, 3, 4, 1 }, { 1, 5, 2, 3, 1 } };
	// 메모리에는 일렬로 쭉 나열됨

	for (int i = 0; i < 2; i++)
	{
		for (int j = 0; j < 5; j++)
		{
			int num = apartment2D[i][j];
			cout << num << " ";
		}
		cout << "\n";
	}

	// 1차원 배열을 보기좋게 늘린거임
	int apartment1D[10] = { 4, 2, 3, 4, 1, 1, 5, 2, 3, 1 };
	for (int i = 0; i < 2; i++)
	{
		for (int j = 0; j < 5; j++)
		{
			int index = (i * 5) + j;
			int num = apartment1D[index];
			cout << num << " ";
		}
		cout << "\n";
	}
}
```

