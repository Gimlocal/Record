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

### nullptr
```cpp
#include <iostream>
#include <vector>
using namespace std;

// nullptr (널 포인터)

class Knight
{
public:
	void Test()
	{

	}
};

Knight* FindKnight()
{

	return nullptr;
}

void Test(int a)
{
	cout << "Test(int)\n";
}

void Test(void* ptr)
{
	cout << "Test(*)\n";
}

// nullptr 구현
const
class
{
public:
	// 어떤 타입의 포인터와도 치환 가능
	template<typename T>
	operator T* () const
	{
		return 0;
	}

	// 어떤 타입의 멤버 포인터와도 치환 가능
	template<typename C, typename T>
	operator T C::* () const
	{
		return 0;
	}
private:
	void operator&() const; // 주소값 &을 막음.
} _NullPtr;

// const NullPtr _NullPtr; 
// 이렇게 변수를 만들수도 있지만 클래스 바로뒤에 붙여서 생성가능
// 또 클래스 이름도 빼도 됨. const도 넣어줄 수 있음

int main()
{
	// 0 NULL
	int* ptr = 0; 
	int* ptr2 = NULL; // 옛날 느낌
	// 0 이든 NULL이든 주소값을 0으로 하는건 같지만 프로그래머들한테 가동성을 위해 NULL을 사용했음

	Test(0);
	Test(NULL);
	// 둘다 int형 버전인 Test가 호출됨
	// 즉 포인터를 활용하는게 아니라 int형 값을 이용한다는 것.

	// 이런 문제때문에 nullptr이 생김
	// nullptr은 오동작, 가독성 측면에서 뛰어남

	Test(nullptr); // 두 번째 버전의 Test가 호출됨.
	Test(_NullPtr);

	auto k = FindKnight();
	if (k == nullptr); // 이처럼 변수가 포인터라는것도 바로 알 수 있음.
	if (k == _NullPtr);

	void(Knight:: * memFunc)();
	memFunc = &Knight::Test;

	if (memFunc == _NullPtr)
	{

	}

	auto a = nullptr; // 타입을 살펴보면 일반적인 타입이 아님 (nullptr_t) 객체임
}
```

### using
```cpp
#include <iostream>
#include <vector>
using namespace std;

// using 

typedef vector<int>::iterator VecIt;
typedef __int64 id;
// 이렇게 typedef로 사용했었음

using id2 = int; // 사용법은 이와 같음

// 뭐가 다른가?

// 1. 직관성이 뛰어남.
typedef void (*Func)(); // 이와같은 함수 포인터는 딱 봤을 때 잘 안 와닿음
using Func2 = void(*)(); // 딱 봐도 함수 포인터임.

// 2. 템플릿
// template<typename T>
// typedef list<T> List;
// typedef는 템플릿을 이용못함
template<typename T>
using List = list<T>;

// 그럼 using이 없었을 때는 템플릿을 어떻게 썼나
// 간접적으로 사용했음.
template<typename T>
struct List2
{
	typedef list<T> L;
};

int main()
{
	id playerId = 0;
	List<int> L;
	List2<int>::L L2;
}
```

### enum class
```cpp
#include <iostream>
#include <vector>
using namespace std;

// enum class

// 기존 enum (기본적으로 int형)
// unscoped enum (범위가 없는)
// 그렇기 때문에 보통 PT와 같이 붙여서 변수 이름을 만들어줌
enum PlayerType : char // 타입을 지정가능
{
	Knight, // 기본값 0부터 시작
	Archer, 
	Mage,
};

enum MonsterType
{
	Knight, // 동일한 이름을 사용하지 못함
};

// 이렇게 하면 범위가 이 enum class 안에서 한정됨
enum class ObjectType
{
	Player,
	Monster,
	Projectile,
};

int main()
{
	// enum class (scoped enum)
	// 1. 이름공간 관리 (scoped)
	// 2. 암묵적인 변환 금지

	int choice; cin >> choice;

	if (choice == static_cast<int>(ObjectType::Player)); // Player는 진짜 ObjectType 타입이기때문에 암묵적인 변환 안됨

	unsigned int bitFlag;
	bitFlag = (1 << static_cast<int>(ObjectType::Player));
}
```

### delete (삭제된 함수)
```cpp
#include <iostream>
#include <vector>
using namespace std;

// delete (삭제된 함수)

class Knight
{
public:

//private:
//	// 정의되지 않은 비공개 함수
//	void operator=(const Knight& k) {} // 즉 이렇게 함수를 만들면 누군가는 꺼내 쓸 수 있음.
//	// 그래서 구현부 없이 그냥 함수만 정의하면 되긴함.
//	// 하지만 Admin에서 복사 생성자를 써놓아도 아무런 문제가 안 생김.
//	friend class Admin; // 이렇게 private이지만 열어줄 수도 있음
public:
	void operator=(const Knight& k) = delete; // delete를 사용한 삭제 함수
	// 보통 public으로 하는게 관례
private:
	int hp = 100;
};

class Admin
{
public:
	void CopyKnight(const Knight& k)
	{
		Knight k1;
		k1 = k;
		// delete를 사용한 삭제함수라서 사용안해도 오류가 발생
	}
};

int main()
{
	Knight k1;
	Knight k2;

	k1 = k2; // 복사 연산자를 못 쓰게 막고 싶다면?
	Admin a;
	a.CopyKnight(k1);
}
```

### overrider, final
```cpp
#include <iostream>
#include <vector>
using namespace std;

// override, final

class Player
{
public:
	virtual void Attack()
	{
		cout << "Player\n";
	}
};

class Knight : public Player
{
public:
	// override(재정의)
	virtual void Attack() final // player에 virtual이 있으면 굳이 여기에 virtual 안붙여도 되긴함.
	{
		cout << "Knight\n";
	}
	// 하지만 Player 클래스를 보고 virtual이 있는지 확인하기 전까진 뭐가 가상 함수인지 모름
	// 즉 어떤 최상위 클래스에서 virtual이 시작되었는지 모름.
	// 또 자식의 virtual 함수에 const같이 다른 기호 하나가 붙으면 아예 다른 함수로 돼서 부모의 함수가 호출됨.
	// 이런 상황에서 override를 사용함. 가독성도 좋아짐.
	// 즉 override를 쓰면 부모에 같은 이름의 virtual 함수가 있다는걸 알려줌.

	// final은 override와 비슷하지만 더 이상 자식에게 override하지 않겠다는 뜻임.
	// 즉 자기가 마지막으로 virtual로 override를 하겠다는 뜻임
};

int main()
{
	Player* p = new Knight();

	p->Attack();
}
```
