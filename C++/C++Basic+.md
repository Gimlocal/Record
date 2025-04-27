# C++ 기초 정리

## 1. 정수 (Integer)

컴퓨터에서 정수를 다루는 기본적인 방법.

| 타입 | 크기 | 범위 |
|:---|:---|:---|
| `char` | 1바이트 | -128 ~ 127 |
| `short` | 2바이트 | 약 -32,768 ~ 32,767 |
| `int` | 4바이트 | 약 -21억 ~ 21억 |
| `__int64` (`long long`) | 8바이트 | 아주 큰 수 |
| `unsigned` 타입 | 양수 전용 |

### 예시 코드
```cpp
short b = 32767;
b += 1;
cout << b << endl; // 오버플로우 발생

unsigned short ub = 0;
ub -= 1;
cout << ub << endl; // 언더플로우 발생
```

### 다이어그램
```
 [ signed short ]
32767 + 1 => -32768 (오버플로우)

 [ unsigned short ]
0 - 1 => 65535 (언더플로우)
```

---

## 2. 불리언과 실수 (Boolean and Floating Point)

- `bool` : 참/거짓 (1바이트)
- `float` : 4바이트 실수 (정밀도 낮음)
- `double` : 8바이트 실수 (정밀도 높음)

### 예시 코드
```cpp
bool flag = true;
float speed = -3.375f;
double speed2 = 1332.4123;

cout << flag << endl; // 1 출력
```

### 다이어그램
```
[ float 구조 ]
부호(1) | 지수(8) | 유효숫자(23)

[ double 구조 ]
부호(1) | 지수(11) | 유효숫자(52)
```

---

## 3. 문자와 문자열 (Character and String)

- `char` : 아스키 문자
- `wchar_t` : 유니코드 문자

### 예시 코드
```cpp
char ch = 'a';
char str[] = "Hello World";
wchar_t wch = L'안';

cout << str << endl;
wcout << wch << endl;
```

### 다이어그램
```
[ 문자열 메모리 구조 ]
'h' 'e' 'l' 'l' 'o' '\0'
```

---

## 4. 데이터 연산 (Data Operations)

- 대입(`=`)
- 산술(`+`, `-`, `*`, `/`, `%`)
- 증감(`++`, `--`)

### 예시 코드
```cpp
int a = 1;
int b = 2;
a = b + 3;
a *= 2;
++a;
```

---

## 5. 비교 연산 & 논리 연산 (Comparison & Logical Operations)

- 비교 : `==`, `!=`, `>`, `<`, `>=`, `<=`
- 논리 : `&&`, `||`, `!`

### 예시 코드
```cpp
bool isSame = (a == b);
bool isAlive = (hp > 0 || isInvincible);
```

### 다이어그램
```
조건 1 (true)
조건 2 (false)
조건 1 && 조건 2 => false
조건 1 || 조건 2 => true
```

---

## 6. 비트 연산 (Bit Operations)

- `~` : NOT
- `&` : AND
- `|` : OR
- `^` : XOR
- `<<` : 왼쪽 쉬프트
- `>>` : 오른쪽 쉬프트

### 예시 코드
```cpp
unsigned char flag = 0;
flag |= (1 << 3); // 무적 상태 추가
bool isInvincible = (flag & (1 << 3)) != 0;
```

### 다이어그램
```
[ 비트 플래그 예시 ]
0b0000 -> 0b1000 (무적 추가)
```

---

## 7. const와 메모리 구조

- `const` : 값 고정
- 메모리 구역
  - `.data` : 초기값이 있는 전역/정적 변수
  - `.bss` : 초기값 없는 전역/정적 변수
  - `.rodata` : 읽기 전용 데이터
  - `stack` : 지역 변수

### 예시 코드
```cpp
const int INVINCIBLE = 3;
flag |= (1 << INVINCIBLE);
```

### 다이어그램
```
[ 메모리 구조 ]
.data -> 초기값 존재
.bss -> 초기값 없음
.rodata -> const 데이터
stack -> 지역 변수
```

---

## 8. 유의사항 (Tips)

- 변수 유효 범위 조심
- 연산 우선순위 애매하면 괄호 사용
- 타입 변환할 때 주의 (데이터 손실 가능)
- 사칙 연산할 때 오버플로우/제로 디비전 주의

### 예시 코드
```cpp
int hp = 77777;
short hp2 = (short)hp; // 데이터 손실

float hp3 = (float)hp;
```

### 다이어그램
```
[int -> short 변환]
77777 -> 값 잘림

[int -> float 변환]
77777 -> 77777.0 (오차 가능)
```

