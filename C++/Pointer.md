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


