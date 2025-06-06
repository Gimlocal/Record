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

