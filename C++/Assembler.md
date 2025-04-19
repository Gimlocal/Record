### 어셈블러 관련

#### 어셈블러 : 일종의 통역기 -> 사람의 말을 컴퓨터의 말로 통역해 작동할 수 있게 해줌.    
<img src="https://github.com/user-attachments/assets/595746a6-900f-4f96-880c-bfae03531050" width="400"/>

#### 메인 메모리(램) : 휘발성 <-> 하드 디스크 : 영구저장    
하지만 메인 메모리는 CPU랑 가까워서 빠름.    
하드 디스크에 내용을 꺼내서 메인 메모리에 복사 이후 실행.    
복사 하는 과정에서 사진과 같이 메모리를 잡음    
<img src="https://github.com/user-attachments/assets/dcd92bb9-f340-483d-a157-85dd7170fba2" width="200"/>    

#### 데이터 : 프로그래밍의 핵심  
#### 비트 바이트 : 컴퓨터의 기초적인 측정 단위 (byte = 8bit)    
 - 16 bit = 2 byte = 1 word
 - 2 word = 1 dword
 - 2 dword = 1 qword
  
2의 보수 -> 최상위 비트를 음수로 사용. 즉 10000000(Byte)는 -128임.    
16진수를 쓰는 이유 : 2진수는 수를 표현하면 너무 길어짐. 그렇다고 10진수를 쓰기에는 컴퓨터 친화적이지 않음. 그래서 그 절충안으로 16진수를 이용.    

#### 레지스터와 메모리 : 접근속도와 저장용량은 반비례. 즉 레지스터는 CPU안에 존재하기 때문에 접근성은 높고 저장용량은 낮다. 그래서 임시 저장용으로 사용됨.    

<img src="https://github.com/user-attachments/assets/887f373d-4e19-4f40-b448-a0272d4ed1b5" width="400"/>    

 - rax : 64bits
 - eax : 32bits
 - ax : 16bits -> ah와 al 8bits로 나눠서 사용
 
하위 비트에서 값 적용 시 상위 비트에는 적용이 안됨
즉 mov eax, 0x1234를 하면 eax 레지스터에 1234가 들어가고 mov al, 0x00을 실행하면 하위 al에서 00이 들어가서 0x1200으로 바뀜    

#### 메모리와 레지스터    
```asm
%include "io64.inc"
section .text
global main
main:
    mov rbp, rsp; for correct debugging

    ; 16 bit = 2 byte = 1 word
    ; 2 word = 1 dword
    ; 2 dword = 1 qword
    
    ; mov reg1, cst
    ; mov reg1, reg2
    
    mov eax, 0x1234
    mov rbx, 0x12345678
    mov cl, 0xff
    
    mov al, 0x00
    
    ; 메모리 -> 레지스터
    ;mov rax, a      ; 실제로는 rax에 a의 주소값이 들어감
    ;mov rax, [a]    ; 이러면 a의 값이 들어감, 크기를 지정안해서 rax의 8바이트만큼 꺼내줌
    mov al, [a]     ;
    
    ; 상수, 레지스터 -> 메모리
    mov [a], byte 0x55
    mov [a], word 0x6666    ; 크기가 안맞으면 옆의 메모리에도 저장
    mov [a], cl

    xor rax, rax
    ret
    
    ; 변수의 선언 및 사용
    ; 변수는 데이터를 저장하는 바구니
    ; 선언(이름, 크기 지정)
    
    ; 메모리에는 구분할 수 있도록 주소(번지수)가 있다
    ; 주소는 프로그램이 실행될 때마다 변할 수 있음
    
    ; 초기화 된 데이터를 쓸 때 data 섹션 사용
    ; [변수이름] [크기] [초기값]
    ; [크기] db(1) dw(2) dd(4) dq(8) : 바이트 크
section .data
    a db 0x11   ; [0x11]로 저장됨
    b dw 0x2222
    c dd 0x33333333
    d dq 0x4444444444444444
    
    
    ; 초기화 되지 않은 데이터 쓸 때 bss 섹션 사용
    ; [변수이름] [크기] [개수]
    ; [크기] resb(1) resw(2) resd(4) resq(8)
section .bss
    e resb 10
```

#### 문자와 앤디안    
```asm
%include "io64.inc"
section .text
global main
main:
    mov rbp, rsp; for correct debugging

    PRINT_STRING msg

    xor rax, rax
    ret

    
    ; 초기화 된 데이터를 쓸 때 data 섹션 사용
    ; [변수이름] [크기] [초기값]
    ; [크기] db(1) dw(2) dd(4) dq(8) : 바이트 크
section .data
    msg db 0x48, 0x65, 0x6c, 0x6c, 0x6f, 0x20, 0x57, 0x6f, 0x72, 0x6c, 0x64, 0x0    ; 아스키 코드를 통해 저장
    a db 0x11, 0x11, 0x11, 0x11   ; [0x11]로 저장됨
    
    b dd 0x12345678 ; 리틀 앤디안 vs 빅 앤디안. 리틀 앤디안이 적용(대부분이 이럼)
    ; 두 방법에는 장단점이 존재.
    ;  리틀 앤디안 : 캐스팅에 유리하다. (데이터 사이즈 변환)
    ;  빅 앤디안 : 숫자 비교에 유리하다. (앞 부분만 보면 되니까)
    
    ; 같은 데이터를 어떻게 분석하냐에 따라 달라짐
    
    ; 초기화 되지 않은 데이터 쓸 때 bss 섹션 사용
    ; [변수이름] [크기] [개수]
    ; [크기] resb(1) resw(2) resd(4) resq(8)
section .bss
    e resb 10
```

#### 사칙연산    
```asm
%include "io64.inc"
section .text
global main
main:
    mov rbp, rsp; for correct debugging

    GET_DEC 1, al
    GET_DEC 1, num
    
    ;PRINT_DEC 1, al
    ;NEWLINE
    ;PRINT_DEC 1, num
    
    ; 더하기 연산
    ; add a, b (a = a + b)
    ; a는 레지스터 or 메모리
    ; b는 레지스터 or 메모리 or 상수
    ; 단 a, b 모두 메모리이면 안됨 
    
    ; 빼기 연산
    ; sub a, b (a = a - b)
    
    add al, 1   ; 레지스터 + 상수
    PRINT_DEC 1, al ; 1 + 1 = 2
    NEWLINE
    
    ;add al, num ; 이렇게 하면 바구니 주소가 사용됨 
    add al, [num]   ; 레지스터 + 메모리
    PRINT_DEC 1, al
    NEWLINE
    
    mov bl, 3   ; 레지스터 + 레지스터
    add al, bl
    PRINT_DEC 1, al
    NEWLINE
    
    add [num], byte 1   ; 메모리 + 상수
    PRINT_DEC 1, [num]
    NEWLINE
    
    add [num], al   ; 메모리 + 레지스터
    PRINT_DEC 1, [num]
    NEWLINE
    
    ; 곱하기 연산
    ; mul reg
    ; - mul bl => al * bl
    ; -- 연산 결과를 ax에 저장 (기본적으로 al을, ax를 사용)
    ; - mul bx => ax * bx
    ; -- 연산 결과를 dx(상위 16비트) ax(하위 16비트)에 저장 
    ; - mul ebx => eax * ebx
    
    ; ex) 5 * 8 은?
    mov ax, 0 ; ax한번 초기화 시켜줌
    mov al, 5
    mov bl, 8
    mul bl  ; 결과물이 ax에 저장됨(40)
    PRINT_DEC 2, ax
    NEWLINE
    
    ; 나누기 연산
    ; div reg
    ; - div bl => ax / bl
    ; -- 연산 결과 : al(몫) ah(나머지)
    
    ; ex) 100 / 3 은?
    mov ax, 100
    mov bl, 3
    div bl
    PRINT_DEC 1, al
    NEWLINE
    mov al, ah
    PRINT_DEC 1, al
    NEWLINE

    xor rax, rax
    ret

    
    ; 초기화 된 데이터를 쓸 때 data 섹션 사용
    ; [변수이름] [크기] [초기값]
    ; [크기] db(1) dw(2) dd(4) dq(8) : 바이트 크
;section .data

    ; 초기화 되지 않은 데이터 쓸 때 bss 섹션 사용
    ; [변수이름] [크기] [개수]
    ; [크기] resb(1) resw(2) resd(4) resq(8)
section .bss
    num resb 1
```

#### 쉬프트 연산 & 논리 연산
```asm
%include "io64.inc"
section .text
global main
main:
    mov rbp, rsp; for correct debugging
    
    ; 쉬프트 연산
    mov eax, 0x12345678
    PRINT_HEX 4, eax
    NEWLINE
    shl eax, 8
    PRINT_HEX 4, eax
    NEWLINE    
    shr eax, 8      ; 8칸 왼쪽, 8칸 오른쪽 쉬프트 한다고 원본 복구가 안됨. 날라감
    PRINT_HEX 4, eax
    NEWLINE         ; 왼쪽 쉬프트는 2배 오른쪽 쉬프트는 1/2배
    
    ; 곱셈/나눗셈
    ; 게임서버에서 ObjectID를 만들어줄 때 쓰일 수 있음 
    
    
    ; 논리 연산
    ; not and or xor
    ; 조건 A, 조건 B
    ; not A : 0이면 1, 1이면 0
    ; A and B : 둘다 1이면 1, 아니면 0
    ; A or B : 둘 중 하나라도 1이면 1, 아니면 0
    ; A xor B : 둘이 같으면 0, 다르면 1
    ; A = 01100110
    ; B = 10111011
    ; A and B = 00100010
    ; A or B = 11111111
    ; A xor B = 11011101
    
    mov al, 0b10010101
    mov bl, 0b01111100
    
    and al, bl  ; al = al and bl
    ; al = 0b00010100 = 0x14
    PRINT_HEX 1, al
    NEWLINE
    
    not al
    ; al = 0b11101011 = 0xeb
    PRINT_HEX 1, al
    NEWLINE
    
    ; 응용 사례 : bitflag(한 비트의 값이 필요할 때)
    
    mov al, 0b10010101
    mov bl, 0b01111100
    
    NEWLINE
    PRINT_HEX 1, al
    NEWLINE
    xor al, bl
    PRINT_HEX 1, al
    NEWLINE
    xor al, bl
    PRINT_HEX 1, al     ; 두 번 xor을 사용하면 원래 값으로 돌아옴 
    NEWLINE             ; 암호학에서 유용함.(key를 이용해 암호화. 이후 다시 복호화)
    
    ; 자기 자신에 xor을 이용하면 항상 0
    
    xor rax, rax
    ret
```




