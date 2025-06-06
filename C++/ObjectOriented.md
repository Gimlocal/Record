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

