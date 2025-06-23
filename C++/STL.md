STL : Standard Template Library

### 벡터(Vector)
```cpp
#include <iostream>
#include <vector>
using namespace std;

// vector

int main()
{
	// STL (Standard Template Library)
	// 프로그래밍할 때 필요한 자료구조/알고리즘을
	// 템플릿으로 제공하는 라이브러리

	// 컨테이너 (Container) : 데이터를 저장하는 객체 (자료구조 : Data Structure)

	// vector (동적 배열)
	// - vector의 동작 운리 (size/capacity)
	// - 중간 삽입 / 삭제
	// - 처음 / 끝 삽입 / 삭제
	// - 임의 접근

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
