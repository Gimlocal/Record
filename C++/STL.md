 STL : Standard Template Library    
 프로그래밍할 때 필요한 자료구조/알고리즘을 템플릿으로 제공하는 라이브러리    

### 벡터(vector) : 기초
```cpp
#include <iostream>
#include <vector>
using namespace std;

// vector

int main()
{
	// 배열
	//const int MAX_SIZE = 10;
	//int arr[MAX_SIZE] = {}; // 배열을 만드는순간 크기가 고정됨. 유동적으로 변경 x

	// 동적 배열
	// 매우 중요한 개념 -> 어떤 방법으로 배열을 유동적으로 사용?
	// 1. 여유분을 두고 메모리를 할당한다.
	// 2. 여유분까지 꽉 찼으면, 메모리를 증설한다.

	int arr[10];
	int* arr2 = new int[100];
	arr2 = new int[1000]; // 메모리 증설
	// Q1. 여유분은 얼만큼인가?
	// Q2. 증설을 얼만큼 하는가?
	// Q3. 기존의 데이터를 어떻게 처리하는가?

	vector<int> v;

	
	// size (실제 사용 데이터 개수)
	// capacity (여유분을 포함한 용량 개수)

	// 만약 증설을 딱 원하는만큼 하려면
	v.reserve(1000);

	// 사이즈를 딱 맞추려면
	v.resize(1000);

	for (int i = 0; i < 1000; i++)
	{
		// 하지만 사이즈를 임의로 resize를 통해서 늘린상태에서 push_back을 하면 그 상태에서 1001번째부터 들어감
		// 사이즈가 할당되어있는 경우에는 직접 접근해 값을 넣어줘야함.
		v[i] = 10;
		//v.push_back(10);
		cout << v.size() << " " << v.capacity() << "\n";
	}
	// 사이즈랑 다르게 capacity는 size가 늘어날수록 많이 커짐
	// 컴파일러에 따라 다르긴하지만 기존 capacity의 절반씩 늘림 : Q2
	// 기존의 데이터는 늘어난 벡터에 복사됨 : Q3
	
	// 하지만 size가 준다고해서 capacity가 줄어들지는 않음.
	v.clear();
	cout << v.capacity() << "\n";

	// 그런데 capacity도 줄이고 싶다면?
	// 임시 벡터를 지정해서 기존 벡터에 넣어줌. 이 벡터는 아무것도 없기 때문에 capacity또한 초기화 됨. 약간의 꼼수
	// 그리고 이 코드가 실행되면 임시벡터(v를 가지고있음, swap으로 바뀌어서)는 사라짐.
	vector<int>().swap(v);

	vector<int> v2(1000, 0); // 정의할 때 size와 초기값들을 지정해줄 수 있음.
	vector<int> v3 = v2; // 복사도 가능함.


	return 0;
}
```

### 벡터(vector) : 반복자(iterator)
```cpp
#include <iostream>
#include <vector>
using namespace std;

// vector

int main()
{
	// 반복자(iterator) : 포인터와 유사한 개념. 컨테이너의 원소(데이터)를 가리키고, 다음/이전 원소로 이동 가능
	vector<int> v(10);
	for (vector<int>::size_type i = 0; i < v.size(); i++)
		v[i] = i;

	vector<int>::iterator it;
	int* ptr;
	
	it = v.begin();	// 이런식으로 넣어줌
	ptr = &v[0];

	cout << (*it) << "\n";
	cout << (*ptr) << "\n";

	ptr++; // 진짜 1이 아니라 타입에 따라 달라짐
	// iterator도 마찬가지
	it++; // 현재 데이터의 다음 데이터를 가리킴

	vector<int>::iterator itBegin = v.begin(); // 이건 첫번째 원소를 가리킴
	vector<int>::iterator itEnd = v.end(); // 하지만 이건 마지막 원소가 아닌 그 다음에있는 쓰레기값을 가리킴. 그래서 보통 끝을 판별할 때 사용함

	// iterator을 이용한 for문
	// ++it 와 it++의 차이 -> f12를 타고가서 보면 it++할때는 자신을 복사해서 ++하고 그걸 반환함. 메모리를 살짝 줄일 수 있음.
	for (vector<int>::iterator it = v.begin(); it != v.end(); ++it)
	{
		cout << *it << "\n";
	}

	// int* ptrBegin = &v[0]; // v.begin._Ptr;
	// int* ptrEnd = ptrBegin + 10; // v.end._Ptr;
	// for (int* ptr = ptrBegin;ptr != ptrEnd;++ptr) 
	// 포인터로도 만들 수 있음.

	// 그래서 iterator를 왜 쓰는가?
	// iterator는 vector뿐 아니라 다른 컨테이너에도 공통적으로 있는 개념.
	// 다른 자료구조에도 적용시켜서 통일성있게 가능함.
	// readonly인 iterator (const_iterator)도 있음
	
	// 역방향 반복자도 있음
	vector<int>::reverse_iterator rit = v.rbegin();
	// v.rend()


	return 0;
}
```

### 벡터(vector) : 데이터 추가, 삭제, 접근
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
	vector<int> v(10);
	v.reserve(1000);
	for (int i = 0; i < 10; i++)
		v[i] = i;

	int* ptrBegin = &v[0];

	// 1. 중간 삽입 / 삭제
	// 2. 처음 / 끝 삽입 / 삭제
	// 3. 임의 접근

	// vector = 동적 배열 = 동적으로 커지는 배열
	// 원소가 하나의 메모리 블록에 연속하게 저장된다.

	// 삽입하려고 하면?
	// 삽입하려는 위치 뒤쪽으로 한 칸 다 밀어야됨.
	// 삭제또한 마찬가지 삭제 위치 뒤쪽 데이터를 한칸씩 다 당겨야함.

	// 1, 2번 중 그나마 끝에 삽입/삭제 하는 것만 깔끔함.
	// 그렇기 때문에 push_back, pop_back 함수를 제공함.
	// 
	// 그럼에도 특정 위치의 데이터를 변경하려면?
	vector<int>::iterator it1 = v.insert(v.begin() + 2, 5); // 3번째 위치에 5를 넣는다.
	vector<int>::iterator it2 = v.erase(v.begin() + 2); // 3번째 위치의 값 삭제
	vector<int>::iterator it3 = v.erase(v.begin() + 2, v.begin() + 4); // 특정 범위 삭제

	// 벡터를 순회하면서 특정 값을 전부 삭제하고 싶다면?
	for (vector<int>::iterator it = v.begin(); it != v.end();)
	{
		int data = *it;
		if (data == 3)
			//v.erase(it); // 이렇게 하면 it의 정보들이 다 날아감. 그리고 다음 과정인 ++it에서 오류가 남.
			it = v.erase(it); // 그래서 그 정보들을 다시 it에 넣어줘야함.
		// 하지만 이렇게 하면 erase하면서 앞으로 땡겨져온 데이터 하나를 체크안하고 넘어감.
		// 그래서 ++it을 위에서 해주면 안되고 따로 조건문에 넣어줘야함
		else ++it;
	}

	// 임의 접근
	// i번째 데이터 -> v[i]로 접근함

	

```
