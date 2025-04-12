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


