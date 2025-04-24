#### 정수
```cpp
#include <iostream>
using namespace std;

int hp = 100;	// 초기값이 있으면 .data 영역 없으면 .bss 영역

char a;		// 1바이트 (-128 ~ 127)
short b;	// 2바이트
int c;		// 4바이트
__int64 d;	// 8바이트(long long)

unsigned char ua;	// 1바이트 (0 ~ 255)
unsigned short ub;	// 2바이트
unsigned int uc;		// 4바이트
unsigned __int64 ud;	// 8바이트(long long)
// unsigned가 붙으면 음수가 아닌 양수만 표현

int main()
{
	b = 32767;
	b += 1;
	cout << b << "\n";  // 0111~1111에서 1000~0000이 됐기 때문에 음수로 바뀜
	
	ub = 0;
	ub -= 1;
	cout << ub;   // 1000~0000에서 0111~1111이 됐기 때문에 양수로 바뀜
}
```
