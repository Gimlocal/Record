### 객체 지향
```cpp
#include <iostream>
using namespace std;

// 객체지향 프로그래밍

// 기존은 절차지향(procedure) 프로그래밍
// 즉 함수들이 메인이 돼서 함수의 흐름의 코드의 흐름이 됨.

// 데이터 + 가공(로직, 동작..)

// 객체지향 = 객체
// 객체란? 플레이어, 몬스터, GameRoom 등등 모든 오브젝트
// class는 일종의 설계도

// ex. Knight를 설계해보자
// - 속성(데이터) : hp, attack, position,
// - 기능(동작) : Move, Attack, Die
class Knight	// cpp에서 struct와 class와 거의 차이가 없음
{
public:
	// 멤버 함수 선언
	void Move(int x, int y);
	void Attack();
	void Die()
	{
		hp = 0;
		cout << "Die" << "\n";
	}
public:
	// 멤버 변수
	int hp;
	int attack;
	int posX;
	int posY;
};

void Knight::Move(int x, int y)	// 정의를 안쪽에 바로 해도 됨
{
	posX = x;
	posY = y;
	cout << "Move" << "\n";
}

void Knight::Attack()
{
	cout << "Attack" << "\n";
}



int main()
{
	Knight k1;	// Instantiate(설계도를 이용해서 객체를 생성)
	k1.hp = 100;
	k1.attack = 10;
	k1.posX = 0;
	k1.posY = 0;

	Knight k2;
	k2.hp = 80;
	k2.attack = 8;
	k2.posX = 1;
	k2.posY = 1;

	k1.Move(2, 2);
	k1.Attack();
	k1.Die();
}
```

### 생성자와 소멸자
```cpp
#include <iostream>
using namespace std;

// 생성자와 소멸자(constructor & Destructor)

// 클래스에 소속된 함수들을 멤버 함수라고 함
// 이 중에서 굉장히 특별한 함수 2종이 있는데,
// 바로 시작과 끝을 알리는 함수들
// 시작 : 생성자, 끝 : 소멸자

// 암시적(implicit) 생성자
// 생성자를 명시적으로 만들지 않으면, 아무 인자도 받지 않는 기본 생성자가 컴퍼일러에 의해 자동으로 만들어짐.
// 그러나 우리가 명시적으로 아무 생성자 하나를 만들면, 자동으로 만들어지던 기본 생성자는 더 이상 만들어지지 않음.
// 그렇게 때문에 인자가 있는 생성자만 만들어두면, Knight(); 를 못함.

class Knight	// cpp에서 struct와 class와 거의 차이가 없음
{
public:
	// 1. 기본 생성자 (인자가 없음)
	Knight() // 객체가 생성되자마자 실행
	{
		cout << "기본 생성자 호출" << "\n";
		hp = 100;
		attack = 10;
		posX = 0;
		posY = 0;
	}

	// 2. 복사 생성자 (자기 자신의 클래스 참조 타입을 인자로 받음)
	// 말 그대로 이미 있는 객체를 복사해서 사용함. 일반적으로 똑같이 만듬
	// 사실 이 부분은 안 만들어도 기본적으로 암시적으로 생성됨.
	// Knight k2(k1)
	// Knight k3 = k1 기본적으로 가능
	// 하지만 Knight k4; k4 = k1; 이 코드는 기본 생성자를 이용해 생성 후 복사를 함.
	Knight(const Knight& knight)
	{
		this->hp = knight.hp;
		this->attack = knight.attack;
		this->posX = knight.posX;
		this->posY = knight.posY;
	}

	// 3. 기타 생성자
	// 이 중에서 인자를 1개만 받는 기타 생성자를 타입 변환 생성자라고 부르기도 함.
	// ... 그래서 명시적으로만 사용가능하게 해야함. 이걸 위한 explicit
	explicit Knight(int hp)
	{
		this->hp = hp;
	}

	// 소멸자
	~Knight() // main함수가 끝나고 실행
	{
		cout << "소멸자 호출" << "\n";
	}

	// 멤버 함수 선언
	void Move(int x, int y);
	void Attack();
	void Die()
	{
		// this는 객체 자신을 가리킴 (Knight)
		this->hp = 0;
		cout << "Die" << "\n";
	}
public:
	// 멤버 변수
	int hp;
	int attack;
	int posX;
	int posY;
};

void Knight::Move(int x, int y)	// 정의를 안쪽에 바로 해도 됨
{
	posX = x;
	posY = y;
	cout << "Move" << "\n";
}

void Knight::Attack()
{
	cout << "Attack" << "\n";
}



int main()
{
	Knight k1;	// Instantiate(설계도를 이용해서 객체를 생성)
	k1.hp = 100;
	k1.attack = 10;
	k1.posX = 0;
	k1.posY = 0;

	k1.Move(2, 2);
	k1.Attack();
	k1.Die();

	Knight k2(k1);

	// 암시적 형 변환 -> 컴파일러가 알아서 바꿔줌
	int num = 1;
	float f = num; // 암시적 형 변환
	float f1 = (float)num; // 명시적 형 변환

	Knight k5;
	//k5 = 1; // 이건 무슨 의미? -> 타입 변환 생성자가 있기 때문에 정수를 이용해서 초기화 가능.
	// 이걸 이용해서 함수 매개변수에 Knight 대신에 정수를 넣어서 호출할 수도 있음. 혼돈 가능
	// 명시적 표시를 해주면 k5 = 1같은 방법은 이제 안됨.
	k5 = (Knight)1; // 이건 가능함. 명시적이니까
}
```

### 상속성
```cpp
#include <iostream>
using namespace std;

// 상속성

// 객체지향
// - 상속성
// - 은닉성
// - 다형성

// 상속(Inheritance)
// 새 직업을 만드는데 기본적으로 멤버들이 똑같음
// 그러면 struct를 만들어서(변수들을) 한번에 넣어주는건 어떨까
// 하지만 이 방법은 함수들을 묶을 수는 없음.

// 그렇기 때문에 공통적인 부분들을 묶어서 가져올 수 있는게 상속.
// 이후 그 클래스에서만 필요한 것들을 추가해주면 됨.

// 생성자(N)/소멸자(1)
// 상속을 받으면 어떻게 되나? -> 둘 다 실행됨. 기존 생/소와 상속받은 생/소 둘다

// 상속의 상속도 가능. 계층구조를 잘 짤 수 있음
// GameObject
// - Creature
// -- Player, Monster, Npc, Pet
// - Projectile
// -- Arrow, Fireball
// - Env


class Player
{
public:
	Player() { cout << "Player 생성자" << "\n"; }
	Player(int hp)
	{
		this->hp = hp;
		cout << "Player hp 생성자" << "\n";
	}
	~Player() { cout << "Player 소멸자" << "\n"; }
	void Move() { cout << "Player Move" << "\n"; }
	void Attack() { cout << "Player Attack" << "\n"; }
	void Die() { cout << "Player Die" << "\n"; }
public:
	int hp;
	int attack;
	int defence;
};

// 상속받은 클래스의 함수를 재정의할 수도 있음.
// 재정의후, 부모의 함수를 사용하고 싶으면 부모::함수 호출로 가능. 하지만 이 방법은 잘 안씀
// 자식의 생성자가 호출되기 전 부모의 생성자가 먼저 실행됨. 선처리 영역이 존재함.
// 소멸자는 그 반대로 호출된 후, 후처리 영역이 존재.

class Knight : public Player
{
public:
	Knight() { cout << "Knight 생성자" << "\n"; }
	Knight(int stamina) : Player(100) // 이와같이 부모의 선처리 영역을 정해줄 수도 있음.
	{
		this->stamina = stamina;
		cout << "Knight stamina 생성자" << "\n";
	}
	~Knight() { cout << "Knight 소멸자" << "\n"; }
	void Move() { cout << "Knight Move" << "\n"; }
public:
	int stamina;
};

class Mage : public Player
{
public:
	int mp;
};

int main()
{
	Knight k(50);
	k.hp = 100;	// 상속받은 모든 멤버들을 사용 가능
	k.attack = 10;
	k.defence = 5;
	
	k.Player::Move();
	k.Move();
	k.Attack();
	k.Die();

	return 0;
}
```

### 은닉성
```cpp
#include <iostream>
using namespace std;

// 은닉성

// 객체지향
// - 상속성
// - 은닉성 = 캡슐화
// - 다형성

// 은닉성 (Data Hiding) = 캡슐화(Encapsulation)
// 몰라도 되는 것은 숨기겠다.
// 왜 숨기냐?
// 1. 위험하고 건드리면 안되는 경우
// 2. 다른 경로로 접근하길 원하는 경우

// ex. 자동차
// - 핸들
// - 페달
// - 엔진
// - 문
// - 전기선

// 일반 구매자 입장에서 사용하는 것 : 핸들, 페달, 문
// 몰라도 됨(오히려 건드리면 안됨) : 엔진, 각종 전기선

// public, protected, private : (멤버)접근 지정자
// public : 모두가 사용 가능
// protected : 상속받은 친구들까지 사용가능
// private : 정의한 클래스 내에서만 사용가능

// 상속 접근 지정자 : 자식한테 어떻게 상속을 해줄 지 정해줌
// - public : 공개적 상속 - 그대로 상속을 다 해줌(private 빼고)
// - protected : 보호받는 상속 - public을 private로 바꿔줌
// - private : 개인적인 상속 - public, protected를 private로 바꿈

class Car
{
public:
	// 일반 사용자가 사용
	void MoveHandle() {}
	void PushPedal() {}
	void OpenDoor() {}
protected:
	// 일반 사용자가 사용x
	void DisassembleCar() {}
	void RunEngine() {}
	void ConnectCircuit() {}
public:
	
};

class SuperCar : public Car // 상속 접근 지정자
{
public:
	void PushRemoteController()
	{
		RunEngine(); // private는 안됨. 그래서 protected로
	}
};


// 캡슐화 : 연관된 데이터와 함수를 논리적으로 묶은 것.
class Berserker
{
public:
	int GetHp() { return hp; }
	int SetHp(int hp)
	{
		this->hp = hp;
		if (hp <= 50) SetBerserkerMode();
	}
private:
	// 사양 : 체력 50이하로 떨어지면 강해짐
	void SetBerserkerMode()
	{
		cout << "Stronger" << "\n";
	}
private:
	// 이 hp를 건드리면 오류가 발생할 수 있음
	// 그래서 private로
	int hp = 100;
};

int main()
{
	SuperCar sCar;
	sCar.PushRemoteController();

	Berserker b;
	b.SetHp(20); // 자동으로 버서커모드 까지 실행
	return 0;
}
```

### 다형성
```cpp
#include <iostream>
using namespace std;

// 다형성

// 객체지향
// - 상속성
// - 은닉성 = 캡슐화
// - 다형성

// 다형성(Polymorphism = 겉은 같은데, 기능은 다르게 동작한다.)
// - 오버로딩(Overloading) = 함수 중복 정의
// - 오버라이딩(Overriding) = 재정의

// 바인딩(Binding) - 묶는다
// 컴파일 과정에서 cpp 코드를 기계코드로 해석할 때 어떤 함수를 어떻게 매핑해줄지 결정하는 것
// - 정적 바인딩(Static Binding) : 컴파일 시점에 결정
// - 동적 바인딩(Dynamic Binding) : 실행 시점에 결정

// 일반 함수는 정적 바인딩 사용
// 동적 바인딩을 하고싶다면? -> 가상 함수 (virtual function)

// 그런데 실제 객체가 어떤 타입인지 어떻게 알고 알아서 가상함수를 호출해준걸까?
//  - 가상 함수 테이블 (vftavble)
//  .vftable [] 4바이트(32) 8바이트(64)
// [VMove] [VDie] 
// 가상 함수를 만들고 메모리를 까보면, Knight의 주소에 가상함수 테이블이 있음
// 이 가상 함수를 찾아주는것은(vftable 설정) 생성자가 해줌.
// Player의 생성자가 먼저 호출돼서 가상 함수 테이블을 설정하고
// 이후 Knight의 생성자가 호출돼서 가상 함수 테이블을 다시 설정해 덮어버림.
// 이 때문에 Knight의 가상 함수들이 호출이 됨.

// 순수 가상 함수 : 구현은 없고 인터페이스만 전달하는 용도로 사용
// 추상 클래스 : 순수 가상 함수가 1개 이상 포함되면 추상 클래스로 간주
// - 직접적으로 객체를 만들 수 없게 됨


class Player
{
public:
	Player()
	{
		hp = 100;
	}

	void Move() { cout << "Move Player" << "\n"; } 
	//void Move(int a) { cout << "Move Player (int)" << "\n"; } // 오버로딩
	virtual void VMove() { cout << "VMove Player" << "\n"; }
	virtual void VDie() { cout << "VDie Player" << "\n"; }

	// 순수 가상 함수
	virtual void VAttack() = 0;
public:
	int hp;
};

class Knight : public Player
{
public:
	Knight()
	{
		stamina = 100;
	}

	void Move() { cout << "Move Knight" << "\n"; } // 오버라이딩
	// 가상함수는 재정의를 하더라도 가상 함수임.
	// 그래서 부모에 virtual이 붙어있기 때문에 자식은 안 붙여도 됨.
	void VMove() { cout << "VMove Knight" << "\n"; }
	void VDie() { cout << "VDie Knight" << "\n"; }
	
	virtual void VAttack()
	{
		cout << "VAttack Knight" << "\n";
	}
public:
	int stamina;
};

class Mage : public Player
{
	int mp;
};


// [ [ Player ] ]
// [   Knight   ]
void MovePlayer(Player* player) // 정적 바인딩으로 인해 무조건 Player Move 실행
{
	player->VMove();
	player->VDie();
}

void MoveKnight(Knight* knight)
{
	knight->Move();
}


int main()
{
	// Player p; // 추상 클래스라서 인스턴스 생성이 안됨
	//MovePlayer(&p); // 플레이어는 플레이어임
	//MoveKnight(&p); // 플레이어는 기사? 아님

	Knight k;
	//MoveKnight(&k); // 기사는 기사임
	// 일반 함수 Move가 정적 바인딩으로 묶여있기 때문에 Knight를 변수로 넣어줘서
	// 실행시켰다 한들 Player 클래스의 Move를 실행
	MovePlayer(&k); // 기사는 플레이어인가? 맞음

}
```

### 초기화 리스트
```cpp
#include <iostream>
using namespace std;

// 초기화 리스트

// 멤버 변수 초기화? 다양한 문법이 존재

// 초기화를 왜 해야할까?
// - 초기화 되지않은 쓰레기 값 때문에 로직이 얽히고 꼬일 수 있음.
// - 버그 예방
// - 포인터 등 주소값이 연루되어 있을 경우

// 초기화 방법
// - 생성자 내에서 초기화
// - 초기화 리스트
// - C++11 문법 (변수를 선언하며 바로 초기화)

// 초기화 리스트
// - 상속 관계에서 원하는 부모 생성자 호출할 때 필요하다
// - 생성자 내에서 초기화 vs 초기화 리스트
// -- 일반 변수는 별 차이 없음
// -- 멤버 타입이 클래스인 경우 차이가 난다
// -- 정의함과 동시에 초기화가 필요한 경우(참조 타입, const 타입)

class Inventory
{
public:
	Inventory() { cout << "Inventory()" << "\n"; }
	Inventory(int size) { cout << "Inventory(int size)" << "\n"; }
	~Inventory() { cout << "~Inventory()" << "\n"; }
public:
	int size = 10;
};

class Player
{
public:
	Player() { cout << "Player()" << "\n"; }
	~Player() { cout << "~Player()" << "\n"; }
};

// Is-A (Knight Is-A Player?) 기사는 플레이어다 -> 상속관계
// Has-A (Knight Has-A Inventory?) 기사는 인벤토리를 가진다 -> 포함관계

class Knight : public Player
{
public:
	Knight() : Player(), hp(50), inventory(20), hpRef(hp), hpConst(100) // 초기화 리스트 방법
	{ 
		cout << "Knight()" << "\n";
		hp = 100; 
	}
	~Knight() { cout << "~Knight()" << "\n"; }
public:
	int hp; // 쓰레기 값
	Inventory inventory; // 이렇게 멤버 변수에 클래스가 있으면 선처리 영역에서 간접적으로 Inventory 생성자를 실행해줌
	// 생성자의 실행 순서는 Player -> Inventory -> Knight 순으로 됨
	// 하지만 Inventory의 다른 생성자를 호출하고 싶으면?
	// Knight의 생성자에서 Inventory의 다른 생성자를 호출하면 Inventory의 생성자가 2번 호출됨. (기본 생성자, 내부에서 호출된 생성자)
	// 호출 순서는 Inventory(기본 생성자 -> 다른 생성자 -> 기본 소멸자 -> 다른 생성자의 소멸자) : 새로운 Inventory가 만들어져 기존의 Inventory를 없앤느낌
	// 그래서 Knight의 선처리 영역에서 다른 생성자를 호출해줘야함
	// 이를 초기화 리스트를 사용해서 생성자를 호출함.

	int& hpRef;
	const int hpConst;
	// 위 두개의 변수는 정의한 순간에 값을 가져야함.
	// 생성자 내부에서 바꾸는게 불가능. 즉 선처리 영역에서 값을 넣어줘야함 (초기화 리스트)
	// 물론 C++11 문법의 정의와 동시에 값을 넣는 방법도 됨.
};

int main()
{
	Knight k;

	cout << k.hp << "\n";
}
```

### 연산자 오버로딩
```cpp
#include <iostream>
using namespace std;

// 연산자 오버로딩(Operator Overloading)

// 연산자 vs 함수
// 연산자는 피연산자의 개수/타입이 고정되어있음.

// 말 그대로 연산에 사용되는 기호들을 오버로딩 하는 것
// 일단 연산자 함수를 정의해야 함.
// 함수도 멤버함수, 전역함수가 존재하는것처럼 연산자 함수도 두 가지 방식이 있음.

// - 멤버 연산자 함수 version
// -- a op b 형태로 왼쪽을 기준으로 실행됨 (a가 클래스여야 가능. a를 기준 피연산자라고 함.)
// -- 한계) a가 클래스가 아니면 안됨. 즉 a + 1은 되지만 1 + a는 안됨.

// - 전역 연산자 함수 version
// -- a op b 형태라면 a, b모두를 연산자 함수의 피연산자로 만들어준다.

// 그럼 무엇이 더 좋나? -> 왕도는 없음. 둘 중 하나가 필요한 경우도 있음
// - 대표적으로 대입 연산자 (a = b)는 전역 연산자 함수로는 불가능

// 복사 대입 연산자 
// 자기 자신의 참조 타입을 인자로 받는 것.
// 특별히 별개로 있는 이유는 말 그대로 객체가 복사되길 원하는 특징 때문

// 기타
// - 모든 연산자를 다 오버로딩 할 수 있는 것은 아님(:: . .* 이런건 안됨)
// - 모든 연산자가 다 2개의 항을 갖지는 않음. 단항 연산자도 있음
// - 증감 연산자++
// -- 전위형 (++a) operator++()
// -- 후위형 (a++) operator++(int)

class Position
{
public:

	// 함수 : 리턴타입 이름(매개변수)
	Position operator+(const Position& pos1)
	{
		Position pos;
		pos.x = x + pos1.x;
		pos.y = y + pos1.y;
		return pos;
	}

	Position operator+(int arg)
	{
		Position pos;
		pos.x = x + arg;
		pos.y = y + arg;
		return pos;
	}

	bool operator==(const Position& pos)
	{
		if (x == pos.x && y == pos.y) return true;
		return false;
	}

	Position& operator=(int arg) // 보통 연산자들은 자기자신을 반환하도록 함.
	{
		x = arg;
		y = arg;
		return *this;
	}

	Position& operator=(const Position& arg) // 복사 대입 연산자
	{
		x = arg.x;
		y = arg.y;
		return *this;
	}

	Position& operator++()
	{
		x++; y++;
		return *this;
	}

	Position operator++(int)
	{
		Position ret = *this;
		x++; y++;
		return ret;
	}
public:
	int x = 0;
	int y = 0;
};

Position operator+(int a, const Position& b)
{
	Position pos;
	pos.x = b.x + a;
	pos.y = b.y + a;

	return pos;
}

//void operator=(const Position& a, int b) // 대입 연산자는 코드의 흐름이 위험해질 수 있어서 멤버 변수로만 가능하게 막아둠.
//{
//	
//}

int main()
{
	Position pos;

	pos.x = 0;
	pos.y = 0;

	Position pos2;
	pos2.x = 1;
	pos2.y = 1;

	Position pos3 = pos + pos2; // 이러한 연산을 하는 연산자가 없음
	Position pos4 = 1 + pos3;

	bool isSame = (pos3 == pos4);

	Position pos5;
	pos3 = pos5 = 5;
	pos3++; ++pos3;

	pos5 = pos3++; // = 연산자와 ++연산자의 타입이 같아야함.
	// 하지만 const Position& = Position 복사값 이렇게 const를 넣으면 복사값도 들어가짐.
}
```

### 객체 지향 마무리
```cpp
#include <iostream>
using namespace std;

// 객체지향 마무리

// 1. struct vs class
// C++ 내에서는 struct나 class나 큰 차이가 없다.
// 차이가 있다면, 접근 지정자의 유무에 따른 차이점
// 접근 지정자가 없으면 struct는 public, class는 private
// 왜 이럴까?
// C++은 C언어에서 파생되어 발전했기 때문에, 호환성을 지키기 위함.
// struct는 일반적으로 그냥 구조체 (데이터 묶음)를 표현하는 용도
// class는 객체 지향 프로그래밍의 특징을 나타내는 용도로 보통 씀

// 2. static 변수, static 함수 (static = 정적 = 고정된)

class Marine
{
public:
	void TakeDamage(int damage) // 한 개체에게만 실행 가능
	{
		hp -= damage;
	}
	static void SetAttack() // 모든 Marine에게 적용 가능
	{
		// hp = 100; 그래서 이런 한 개체의 hp를 건드는 것은 불가능 (hp는 한 객체에 종속적이니까)
		attack = 11;
	}
public:
	int hp; // 얘는 실시간으로 바뀔 수 있지만(종속적)
	static int attack; // 얘는 항상 고정 혹은 다 같이 올라감(클래스 자체와 연관됨, 메모리에 한개만 생김)
};

// static 변수는 어떤 메모리?
// 초기화 하면 .data 안 하면 .bss
int Marine::attack = 0; // 외부 선언하면 누구나 다 접근할 수 있게됨.

class Player
{
public:
	int id;
};

int GenerateId()
{
	// 생명주기 : 프로그램 시작/종료 (메모리에 항상 올라가 있음)
	// 가시범위 : 함수 내부

	// 정적 지역 객체
	static int s_id = 1;
	return s_id++;
}

int main()
{
	Marine m1;
	m1.hp = 40;

	Marine m2;
	m2.hp = 40;

	Marine::attack = 10; // 이렇게 할 수 있음

	// 공격력을 올려야 하면? -> 모든 마린의 공격력을 하나하나 올려야 하나?
	// 즉 모든 마린의 공격력을 똑같이 설정하고 싶음 -> static 문법

	static int id = 10; // 함수 내부에 만들어도 스택이 아니라 데이터 영역에 들어감

	cout << GenerateId() << "\n";
	cout << GenerateId() << "\n";
	cout << GenerateId() << "\n";
	cout << GenerateId() << "\n";
	cout << GenerateId() << "\n";
}
```
