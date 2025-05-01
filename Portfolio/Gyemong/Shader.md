### 셰이더 구현
#### 셰이더란?    
- #### 셰이더(Shader)란 3D 컴퓨터 그래픽에서 물체의 3차원 위치를 나타내는 x, y, z 좌표나, 색상, 텍스처, 조명 등 다양한 시각적 효과를 계산하여 최종적으로 화면에 출력할 픽셀의 위치와 색상을 계산하는 함수입니다.
다양하고 많은 종류의 셰이더가 존재하지만, 제가 구현한 셰이더는 원하는 색의 외곽선을 추가해주는 쉐이더 입니다.    
셰이더는 코드를 통해서도 구현할 수 있지만, 저는 에디터 내부에서 Shader Graph를 이용해 만들어봤습니다.    

Shader Graph에 관한 설명이나 설정 등은 아래 링크를 통해 참고했습니다.    
https://rusalgames.tistory.com/34

----

![image](https://github.com/user-attachments/assets/2359e56f-33d4-4273-aa39-ec55b63c4060)    
일단 전체 형태는 위와 같습니다. Main Preview에 볼 수 있듯이 초록색 외곽선이 생긴걸 알 수 있습니다.    
그럼 하나씩 살펴보도록 하겠습니다.    

- #### 원리
구현에 들어가기 앞서 구현 원리를 간단하게 설명드리자면    
  1. 원본 이미지4개를 동서남북 방향으로 적당히 이동시킨다.
  2. 4개의 이미지를 합치고 원본이미지 부분만큼 제거한다.
  3. 남는 부분에 원하는 색을 입히고 원본 이미지와 다시 합친다.    

와 같은 방식으로 진행됩니다.

- #### 텍스쳐 가져오기
![image](https://github.com/user-attachments/assets/1013170c-ddbc-421d-8e08-2c4f21405176)    
먼저 Sample Texture 2D 노드를 만든 후, 적용시킬 Texture를 Texture(T2)에 넣어줍니다. 그러면 위의 이미지와 같이 Texture그림이 보이게 될겁니다.    
다만 버그로 인해서 Texture가 뭉개져서 보이는데, 이 부분은 뒤에 부분에서 더 설명하겠습니다.    
Sample Texture 2D옆에 있는 Tiling And Offset이라는 노드는 말 그대로 길이를 늘리거나 위치를 이동시키는 노드입니다.    

- #### 이미지 이동시키고 합치기
![image](https://github.com/user-attachments/assets/a0c42615-68a3-48a7-9d9c-885c569b1138)    
위와 같이 x축으로 1, y축으로 0의 벡터를 OutLineThickness라는 상수와 곱해서 그 만큼 이동시켜주도록 했습니다. 즉 동쪽으로 OutLineThickness만큼 이동시켜준거죠    
이 작업을 4번 진행합니다.    
![image](https://github.com/user-attachments/assets/a9d28201-c024-4ac2-b716-6a6ca71d7b83)     
4번 진행한 Sample Texture 2D에서 A값을 각각 가져온 후 Add노드를 통해 4개의 이미지를 합쳐주겠습니다.    
RGBA(삼원색 + alpha값)중에서 A만 쓴 이유는 지금은 색이 아니라 이미지의 실루엣만 필요하기 때문입니다.    
Add노드는 한번에 두개씩만 합칠 수 있기 때문에 두번 사용했습니다.    

- #### 외곽선 따고 색 입히기
![image](https://github.com/user-attachments/assets/df0b2711-ead0-40f2-8fca-ba5b9ad71dac)    
4개의 이미지를 합친 이미지를 sub노드를 통해서 원본 이미지만큼 제거합니다.    
![image](https://github.com/user-attachments/assets/733ae403-7d09-4b8a-af90-6a420f96d247)    
이후 색을 입힙니다.    

- #### 원본 이미지와 합치기
![image](https://github.com/user-attachments/assets/aa109f15-9094-4372-bb81-fc5aeed5aaf6)     
원본 이미지가 뭉개지는 현상은 위와 같이 원본 이미지에 원본 이미지의 A값을 한번 더 곱해주면 정상적으로 이미지가 나옵니다.     
![image](https://github.com/user-attachments/assets/8d898c56-8efa-41b6-9c5c-9f0ba5dd5555)    
이후 외곽선과 원본 이미지를 합쳐줍니다.    
![image](https://github.com/user-attachments/assets/186a3f98-b332-4bfb-9e0a-71e9887eba57)    
여기서 조건에 따라 외곽선을 켰다 껐다 하고싶으면 Branch노드를 통해서 조정해주면 됩니다.    
![image](https://github.com/user-attachments/assets/37ca55bc-2fe9-4247-b6af-985d31b13973)
