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

#### 반복문
```cpp
#include <iostream>

using namespace std;

// 반복문


int main()
{
	// while
	// if - else도 유용하지만
	// 한 번만 실행하는게 아니라 특정 조건까지 계속 반복하는 경우
	// ex) 게임을 끌 때까지 계속 게임 실행
	// ex) 목적지에 도달할 때까지~ 이동

	// while (조건식)
	//{
	//	명령문
	//}

	int count = 0;

	while (count < 5)
	{
		cout << "Hello Wolrd\n";
		count++;
	}

	do	// 무조건 한번은 실행됨
	{
		cout << "Hello Wolrd\n";
		count++;
	} while (count < 5);

	// for (초기식1 ; 조건식2; 제어식4) 명령3 (번호순대로 실행. 1은 처음 한번만)
	// while문 보다 실수를 줄일 수 있음. 제어식이 필수니까.

	for (int count = 0; count < 5; count++)
	{
		cout << "Hello World\n";
	}

	// for문 내에서 특정 조건을 만족할 때 for문을 벗어나거나
	// 다음 분기로 이동하고싶을 때, break, continue를 사용
}
```


