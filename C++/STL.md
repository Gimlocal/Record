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

### 리스트(List) : 기초
```cpp
#include <iostream>
#include <list>
using namespace std;

// List

// vector : 동적 배열
// [      ] 자리가 부족하면 capacity늘림 1.5배로 그리고 데이터 이전


// list : 연결 리스트
// 단일 / 이중 / 원형

// [1] [2] [3] [4] [5] 값 저장은 비슷함.
// 하지만 이 데이터들이 연속된 공간에 저장되는건 아님.
// 즉 다음 데이터를 가리키는 포인터 형식의 데이터가 있고 이 데이터를 통해서 다음 값으로 넘어감

class Node // 이런 느낌의 데이터가 저장됨. 값이 data고 다음 값을 가리키는 포인터가 next임
{
public:
	Node* next; // 이 부분에 Node로 만들면 정의가 안됨. 포인터(주소값)이라서 괜찮
	Node* prev;
	int data;
};
// 그리고 list는 이 Node들의 모임.

// 이때 단방향으로만 연결되어있으면 단일 연결 리스트
// 양방향으로 연결되면 이중 연결 리스트. -> 이게 list 형태
// 처음과 끝이 이어져있으면 원형 리스트.


// [1] <-> [2] <-> [3] <-> [4] <-> [5] 여기서 3자리에 100을 추가하려면?
// 2의 next를 100으로, 100의 next를 3으로 지정하면 됨. 간단 (prev도 마찬가지)
// 그래서 처음/끝 삽입/삭제또한 마찬가지로 간단하게 next,prev를 이용해서 사용가능

// 하지만 임의접근은 좀 애매함.
// 4번째 요소를 찾으려면?
// count를 하나씩 하면서 next를 돌려야함.

int main()
{
	// list (연결 리스트)
	// - list의 동작 원리
	// - 중간 삽입/삭제 (good)
	// - 처음/끝 삽입/삭제 (good)
	// - 임의 접근 (bad)

	list<int> l;
	for (int i = 0; i < 100; i++)
		l.push_back(i);
	//l.push_front(10); // 벡터와 다르게 앞에도 추가 가능.
	int size = l.size();
	int first = l.front();
	int last = l.back();

	// l[1] 벡터와는 다르게 인덱스를 이용해서 접근 안됨.

	list<int>::iterator itBegin = l.begin(); // 이터레이터 사용법은 동일
	list<int>::iterator itEnd = l.end();

	int* ptrBegin = &(l.front());
	int* ptrEnd = &(l.back());

	for (list<int>::iterator it = l.begin(); it != l.end(); ++it)
		cout << *it << "\n";

	//l.insert(itBegin, 100); // itBegin위치에 삽입

	//l.erase(l.begin());

	//l.pop_front();

	//l.remove(10); // 모든 10 삭제


	// 임의 접근이 안되는데 중간 삽입/삭제가 빠르다?
	// 중간에 값에 접근부터 해야되는데?

	// 50번 인덱스에 있는 데이터를 삭제
	list<int>::iterator it = l.begin();
	for (int i = 0; i < 50; i++)
		++it;
	l.erase(it); // 이렇게 하면 결국 똑같지 않나
	// 맞음 그렇기 때문에 특정 인덱스의 데이터를 찾아서 삭제 -> 빠른건 아님.
	// erase하는거 자체가 빠른거임.
	// 중간 삽입/삭제를 빠르게 하는건 push_back을 할 때 insert를 이용해서 특정 인덱스의 요소를 iterator로 기억해두면 빠르게 가능함.
}
```

### 리스트(List) : 구현
```cpp
#include <iostream>
#include <list>
using namespace std;

// list

template<typename T>
class Node
{
public:
	Node() : next(nullptr), prev(nullptr), data(T()) { }
	Node(const T& value) : next(nullptr), prev(nullptr), data(value) { }
public:
	Node*	next;
	Node*	prev;
	T		data;
};

template<typename T>
class Iterator
{
public:
	Iterator() : node(nullptr) { }
	Iterator(Node<T>* node) : node(node) { }

	Iterator& operator++() //++it
	{
		node = node->next;
		return *this;
	}
	Iterator operator++(int) // it++
	{
		Iterator<T> temp = *this;
		node = node->next;
		return temp;
	}
	Iterator& operator--() //--it
	{
		node = node->prev;
		return *this;
	}
	Iterator operator--(int) // it--
	{
		Iterator<T> temp = *this;
		node = node->prev;
		return temp;
	}

	T& operator*()
	{
		return node->data;
	}

	bool operator==(const Iterator& right)
	{
		return node == right.node;
	}

	bool operator!=(const Iterator& right)
	{
		return node != right.node;
	}
public:
	Node<T>* node;
};

template<typename T>
class List
{
public:
	List() : _size(0)
	{
		header = new Node<T>();
		header->next = header;
		header->prev = header;
	}

	~List()
	{
		while (_size > 0)
			pop_back();

		delete header;
	}

	void push_back(const T& value)
	{
		AddNode(header, value);
	}

	void pop_back()
	{
		RemoveNode(header->prev);
	}

	Node<T>* AddNode(Node<T>* before, const T& value)
	{
		Node<T>* node = new Node<T>(value);
		
		Node<T>* prevNode = before->prev;
		prevNode->next = node;
		node->prev = prevNode;
		node->next = before;
		before->prev = node; // before와 prevNode 사이에 node 끼워넣기

		_size++;

		return node;
	}

	Node<T>* RemoveNode(Node<T>* node)
	{
		Node<T>* prevNode = node->prev;
		Node<T>* nextNode = node->next;
		
		prevNode->next = nextNode;
		nextNode->prev = prevNode;

		delete node;
		_size--;
		
		return nextNode;
	}

	int size() { return _size; }

public:
	typedef Iterator<T> iterator;
	iterator begin() { return iterator(header->next); }
	iterator end() { return iterator(header); }
	iterator insert(iterator it, const T& value)
	{
		Node<T>* node = AddNode(it.node, value);
		return iterator(node);
	}
	iterator erase(iterator it)
	{
		Node<T>* node = RemoveNode(it.node);
		return iterator(node);
	}

public:
	Node<T>* header;
	int _size;
};


int main()
{
	List<int> l;
	List<int>::iterator eraseIt;

	for (int i = 0; i < 10; i++)
	{
		if (i == 5)
			eraseIt = l.insert(l.end(), i);
		else
			l.push_back(i);
	}

	l.pop_back();

	l.erase(eraseIt);

	for (List<int>::iterator it = l.begin(); it != l.end(); ++it)
		cout << *it << "\n";
}
```

### 디큐(deque)
```cpp
#include <iostream>
#include <vector>
using namespace std;
#include <deque>

// deque

int main()
{
	// 시퀀스 컨테이너 (Sequence Container)
	// 데이터가 삽입 순서대로 나열되는 형태
	// vector list deque

	// vector : 동적 배열
	// [        ]
	// list : 이중 연결 리스트
	// [ ] <-> [ ] <-> [ ]
	// deque : double-ended queue 데크
	// [    ]
	// [    ]


	// vector와 마찬가지로 배열 기반으로 동작
	// 다만 메모리 할당 정책이 다르다
	// 블록 단위로 메모리가 떨어져 있음.
	// 데이터가 꽉 차면 새로운 공간을 만들고 그곳에 추가로 채움.
	// 즉 처음있던 공간은 그대로 두고 새로운 공간을 추가로 더 만드는 느낌


	vector<int> v(3, 1);
	deque<int> dq(3, 1);

	v.push_back(2);
	v.push_back(2);

	dq.push_back(2);
	dq.push_back(2);

	dq.push_front(3);
	dq.push_front(3);

	// deque의 동작 원리
	// 중간 삽입/삭제 (vector와 동일하게 느림. 앞으로 땡기는 작업 필요. 물론 앞이나 뒤 중 적은 방향으로 땡기긴 하지만 그래도 느림)
	// 처음/끝 삽입/삭제 (분리된 공간이 있기 때문에 good)
	// 임의 접근 (가능)
}
```

### 맵(map)
```cpp
#include <iostream>
using namespace std;
#include <map>

// map

int main()
{
	// vector에서는 특정 값을 찾기위해서는 무조건 순회를 해야함. 비효율적임
	// 즉 원하는 조건에 해당하는 데이터를 빠르게 찾기 힘들다.

	// 그래서 나온게 연관 컨테이너

	// map : 균형 이진 트리 (AVL)
	// - 노드 기반

	class Node
	{
	public:
		Node* left;
		Node* right;
		int key;
		// Player* value;

	};

	srand(static_cast<unsigned int>(time(nullptr)));
	map<int, int> m;
	for (int i = 0; i < 100000; i++)
	{
		m.insert(pair<int, int>(i, i * 100));
	}

	for (int i = 0; i < 50000; i++)
	{
		int ran = rand() % 50000;
		m.erase(ran);
	}

	map<int,int>::iterator it = m.find(10000);
	if (it != m.end()) cout << "찾음\n";
	else cout << "못찾음\n";

	// erase를 똑같이 두 번 하면 두 번째는 무시됨.
	// insert를 똑같은 key에 다른 value로 두 번 하면 마찬가지로 두 번째는 무시됨

	// map 순회
	for (map<int, int>::iterator it = m.begin(); it != m.end(); ++it)
	{
		int key = (*it).first;
		int val = (*it).second;
		cout << key << "  " << val << " \n";
	}

	// 없으면 추가, 있으면 수정
	map<int, int>::iterator it = m.find(10000);
	if (it != m.end()) it->second = 200;
	else m.insert({ 10000, 200 });

	// 더 쉬운버젼
	m[10000] = 200; // key가 10000인 놈이 있으면 val을 200으로 만들고, 없으면 새로 만들어서 200을 넣음

	m.clear();

	m[10]; // 아무것도 없을 때 값이 있는지 확인하려는 순간 데이터가 들어감.

	// 결론 : 특정 데이터를 찾는 부분에 있어서는 개빠름.
}
```

### 셋(set), 멀티맵(multimap), 멀티셋(multiset)
```cpp
#include <iostream>
#include <vector>
#include <set>
#include <map>
using namespace std;

// set, multimap, multiset

int main()
{
	// map : key - value로 이루어짐.

	// set : key 하나만 사용(key == value 느낌)
	set<int> s;

	// 넣고, 빼고, 찾고, 순회
	s.insert(5);
	s.insert(15);
	s.insert(6);
	s.insert(12);
	s.insert(9);

	s.erase(6);
	s.erase(12);

	set<int>::iterator findIt = s.find(15);
	if (findIt == s.end())
	{
		cout << "No\n";
	}
	else cout << "Yes\n";

	for (set<int>::iterator it = s.begin(); it != s.end(); ++it)
	{
		cout << *it << "\n";
	}

	// multi가 붙으면 중복을 허용한다는 뜻.
	
	// multimap
	multimap<int, int> mm;
	mm.insert(make_pair(3, 19));
	mm.insert(make_pair(3, 42));
	mm.insert(make_pair(3, 191));
	mm.insert(make_pair(1, 49));
	mm.insert(make_pair(1, 4));

	// mm[1] 이거 안됨. 여러개가 있을 수 있어서
	mm.erase(1); // key == 1인 모든 value 삭제 반환값이 int (삭제 개수)

	mm.find(1); // 들어온 순서대로 찾아서 가장 먼저 걸린 놈 삭제 (1개 삭제)
	mm.equal_range(1); // key가 1인 값들을 사이에 두는 iterator 2개인 pair로 반환함


	// multiset
	multiset<int> ms;
	ms.insert(100);
	ms.insert(100);
	ms.insert(100);
	ms.insert(2);
	ms.insert(2);

	multiset<int>::iterator findIt = ms.find(100);

	ms.equal_range(2);
	// multimap과 동일하게 사용
	// equal_range로 pair로 된 iterator을 받기 귀찮으면
	ms.lower_bound(100); // 100이 시작하는 iterator
	ms.upper_bound(100); // 마지막 100 다음 iterator
	// 두개를 이용해서 iterator 순회로 사용해도 됨
}
```

### 연습문제
```cpp
#include <iostream>
#include <vector>
using namespace std;

// 연습문제

int main()
{
	srand(static_cast<unsigned int>(time(nullptr)));
	vector<int> v;

	for (int i = 0; i < 100; i++)
	{
		int num = rand() % 100;
		v.push_back(num);
	}

	// 1. number라는 숫자가 벡터에 있는지 확인
	{
		int number = 50;
		bool found = false;

		for (int i = 0; i < v.size(); i++)
		{
			if (v[i] == number)
			{
				found = true;
				break;
			}
		}
		string a = found ? "Yes" : "No";
		cout << a << "\n";
	}

	// 2. 11로 나뉘는 숫자가 벡터안에 있는지 체크하는 기능
	{
		bool found = false;
		for (int i = 0; i < v.size(); i++)
		{
			if (v[i] % 11 == 0)
			{
				found = true;
				break;
			}
		}
		string a = found ? "Yes" : "No";
		cout << a << "\n";
	}

	// 3. 홀수인 숫자의 개수는?
	{
		int count = 0;
		for (int i = 0; i < v.size(); i++)
		{
			if (v[i] % 2 == 1) count++;
		}
		cout << count << "\n";
	}

	// 4. 벡터에 들어가 있는 모든 숫자들에 3을 곱하기
	{
		for (int i = 0; i < v.size(); i++)
		{
			v[i] *= 3;
			cout << v[i] << " ";
		}
	}

	// 이런 구조들은 vector일때만 잘 적용이 됨.
	// 만약 자료구조를 list로 작성했다면? -> index를 통한 임의접근이 안됨. 망함
}
```

### 알고리즘
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

// 알고리즘

int main()
{
	// 자료구조 (데이터를 저장하는 구조)
	// 알고리즘 (데이터를 어떻게 사용할 것인가)

	// find
	// find_if
	// count
	// count_if
	// all_of
	// any_of
	// none_of
	// for_each
	// remove
	// remove_if

	srand(static_cast<unsigned int>(time(nullptr)));
	vector<int> v;

	for (int i = 0; i < 100; i++)
	{
		int num = rand() % 100;
		v.push_back(num);
	}

	// 1. number라는 숫자가 벡터에 있는지 확인
	{
		int number = 50;
		bool found = false;

		vector<int>::iterator it = find(v.begin(), v.end(), number);
		// 보통 iterator을 자료구조에서 쓰니까 공용으로 사용가능
		found = it == v.end() ? false : true;
		string a = found ? "Yes" : "No";
		cout << a << "\n";
	}

	// 2. 11로 나뉘는 숫자가 벡터안에 있는지 체크하는 기능
	{
		bool found = false;
		
		struct CanDivideBy11 // 함수 객체로 넣어주기
		{
			bool operator()(int n)
			{
				return (n % 11) == 0;
			}
		};
		vector<int>::iterator it = find_if(v.begin(), v.end(), CanDivideBy11());
		found = it == v.end() ? false : true;
		string a = found ? "Yes" : "No";
		cout << a << "\n";
	}

	// 3. 홀수인 숫자의 개수는?
	{
		struct IsOdd
		{
			bool operator()(int n)
			{
				return (n % 2) == 1;
			}
		};
		int count = count_if(v.begin(), v.end(), IsOdd());

		all_of(v.begin(), v.end(), IsOdd()); // 모든 수가 홀수인지
		any_of(v.begin(), v.end(), IsOdd()); // 하나라도 홀수인지
		none_of(v.begin(), v.end(), IsOdd()); // 홀수가 하나도 없는지
		// 위 3개는 반환값이 bool

		cout << count << "\n";
	}

	// 4. 벡터에 들어가 있는 모든 숫자들에 3을 곱하기
	{
		struct Mul3
		{
			void operator() (int& n)
			{
				n *= 3;
			}
		};
		for_each(v.begin(), v.end(), Mul3());
	}

	// 5. 홀수인 데이터를 일괄 삭제
	{
		struct IsOdd
		{
			bool operator()(int n)
			{
				return (n % 2) == 1;
			}
		};
		remove(v.begin(), v.end(), 4);
		remove_if(v.begin(), v.end(), IsOdd());
		// 로직은 필요한 데이터들만 남기는 느낌

		// 1 4 3 5 8 2 로 시작한다고 가정하면 (IsOdd 버전)
		// 1이 홀수이므로 이 뒤에서부터 시작해서 IsOdd에 안 걸리는 숫자를 찾음
		// 바로 뒤에 4가 걸림. 그러면 이 4를 1자리에 이동시킴
		// 4 4 3 5 8 2
		// 동일하게 뒤로 한칸 씩 가면서 짝수들을 그 다음자리에 넣어줌
		// 최종적으로 4 8 2 5 8 2 라는 배열로 바뀜
		// 그리고 마지막에 불필요한 데이터의 위치를 뱉어줌 4 8 2까지가 필요하고 5부터 불필요함.
		// 5의 iterator를 뱉음
		// 이후에 사용자가 반환받은 iterator을 가지고 erase를 통해 뒷부분을 지워줘야 완벽해짐.

		v.erase(remove_if(v.begin(), v.end(), IsOdd()), v.end());
		// 그래서 이렇게 세트로 많이 씀
	}
}
```
