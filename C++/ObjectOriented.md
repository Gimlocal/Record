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

