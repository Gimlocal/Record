#### 분기문
```cpp
#include <iostream>
using namespace std;

// 분기문
// 데이터를 메모레이 할당하고 가공하는 방법을 알아봤음
// 가공한 데이터를 이용해서 무엇인가를 하고 싶다면?


int main()
{
	int hp = 100; // 몬스터 HP
	int damage = 100; // 플레이어 데미지

	hp -= damage; // 플레이어가 몬스터 공격
	bool isDead = (hp <= 0); // 처치 판정

	// 몬스터가 죽었으면 경험치 추가
	// 어셈블리에서 CMP JMP를 이용

	// if (조건)
	//	명령
	if (isDead)	// 0이 아니면 무조건 실행
	{
		cout << "몬스터 처치" << "\n";
	}
	//if (isDead == false)	// (!isDead)와 동일
	else if (hp <= 20)
		cout << "몬스터 도망\n";
	else
		cout << "몬스터가 반격\n";


	const int ROCK = 0;
	const int PAPER = 1;
	const int SCISSORS = 2;

	int input = ROCK;

	if (input == ROCK)
		cout << "바위\n";
	else if (input == PAPER)
		cout << "보\n";
	else if (input == SCISSORS)
		cout << "가위\n";
	else
		cout << "?\n";

	// 반복되는 부분이 너무 많음 input ==

	// switch-case 문
	// 정수 계열만 넣을 수 있음
	switch (input)
	{
	case ROCK:
		cout << "바위\n";
		break;
	case PAPER:
		cout << "보\n";
		break;
	case SCISSORS:
		cout << "가위\n";
		break;
	default:	// 위의 경우들를 제외한 모든 경우
		cout << "?\n";
	}
}
```


