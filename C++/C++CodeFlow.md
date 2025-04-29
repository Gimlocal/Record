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

#### 연습문제1
```cpp
#include <iostream>
using namespace std;

int main()
{
	// 별찍기
	// N*N개의 별을 찍기
	int input;
	cin >> input;
	for (int i = 0; i < input; i++)
	{
		for (int j = 0; j < input; j++)
		{
			cout << "*";
		}
		cout << "\n";
	}

	// 1개부터 시작해서 순차적으로 증가하는 별 찍기
	for (int i = 0; i < input; i++)
	{
		for (int j = 0; j < i+1; j++)
		{
			cout << "*";
		}
		cout << "\n";
	}

	// N개부터 시작해서 순차적으로 감소하는 별 찍기
	for (int i = 0; i < input; i++)
	{
		for (int j = 0; j < input-i; j++)
		{
			cout << "*";
		}
		cout << "\n";
	}

	// 2단부터 9단까지 구구단을 출력
	for (int i = 2; i < 10; i++)
	{
		for (int j = 1; j < 10; j++)
		{
			cout << i << " x " << j << " = " << i * j << "\n";
		}
	}
}
```

#### 연습문제2
```cpp
#include <iostream>
using namespace std;

int main()
{
	srand(time(0)); // 시드설정 (안 하면 항상 같은 값으로 나옴)
	// 랜덤 값 
	int val = rand();	// 0 ~ 32767사이의 값
	int ran = 1 + rand() % 3;	// 1 ~ 3사이의 값

	int wins = 0;
	int total = 0;

	while (true)
	{
		if (total != 0)
			cout << "현재 승률 : " << (float)wins / (float)total * 100 << "%" << "\n";

		cout << "\n가위(1) 바위(2) 보(3) 골라\n";
		int input;
		cin >> input;

		if (input < 1 || input > 3) break;

		

		int ranVal = 1 + rand() % 3;
		switch (ranVal)
		{
		case 1:
			if (input == 1)
				cout << "가위(너) vs 가위(컴퓨터) 비겼습니다.\n";
			else if (input == 2)
			{
				cout << "바위(너) vs 가위(컴퓨터) 이겼습니다.\n";
				wins++;
			}
			else
				cout << "보(너) vs 가위(컴퓨터) 졌습니다.\n";
			total++;
			break;
		case 2:
			if (input == 2)
				cout << "바위(너) vs 바위(컴퓨터) 비겼습니다.\n";
			else if (input == 3)
			{
				cout << "보(너) vs 바위(컴퓨터) 이겼습니다.\n";
				wins++;
			}
			else
				cout << "가위(너) vs 바위(컴퓨터) 졌습니다.\n";
			total++;
			break;
		case 3:
			if (input == 3)
				cout << "보(너) vs 보(컴퓨터) 비겼습니다.\n";
			else if (input == 1)
			{
				cout << "가위(너) vs 보(컴퓨터) 이겼습니다.\n";
				wins++;
			}
			else
				cout << "바위(너) vs 보(컴퓨터) 졌습니다.\n";
			total++;
			break;
		default:
			cout << "제대로 내자\n";
		}
	}
}
```

#### 열거형
```cpp
#include <iostream>
using namespace std;

const int SCISSORS = 1;
const int ROCK = 2;
const int PAPER = 3;
// 이렇게 하면 상수로 되긴하는데, 너무 따로 노는 느낌
// 대신에 enum이라는 열거형 변수를 만듬

enum ENUM_SRP	// 초기값 설정을 안해줘도됨. 자동으로 0부터 지정됨 (지정안할경우)
{
	ENUM_SCISSORS = 1,
	ENUM_ROCK = 2,
	ENUM_PAPER = 3,
};

#define DEFINE_SCISSORS 1;	
// 이렇게 정의할 수도 있음. # -> 전처리 지시문
// 빌드는 전처리 - 컴파일 - 링크 순서로 이루어짐.
// #include <iostream>이라는 파일을 찾아서 해당 내용을 그냥 복붙

int main()
{
	int a;
	cin >> a;
	switch (a)
	{
	case ENUM_SCISSORS:
		break;
	case ENUM_ROCK:
		break;
	case ENUM_PAPER:
		break;
	default:
		break;
	}
}
```

