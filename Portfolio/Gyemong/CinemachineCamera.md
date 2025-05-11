### 시네머신 카메라    
**시네머신**이란 유니티에서 제공하는 패키지로, Package Manager에서 다운로드 받아서 사용할 수 있습니다.    
시네머신은 다양하고 유용한 기능을 가진 카메라들을 제공합니다.    
사용법은 간단합니다.   

![image](https://github.com/user-attachments/assets/9de45097-d131-4c15-b1eb-d1eca6c11b1b)    

이미지처럼 하이라키에서 우클릭 후, 원하는 카메라를 생성하시면 됩니다.    

종류가 아주 다양한데 조금 설명을 드리자면    
**2D Camera** : 직교 뷰로 사용되는 카메라.   
**Blend List Camera** : 할당된 버추얼 카메라들을 정해진 블랜드 방식에 따라 순차적으로 전환하는 카메라.    
**ClearShot Camera** : 플레이어의 충돌/트리거 상태에 따라 활성화/비활성화 시킬 수 있는 카메라.    
**Dolly Camera** : 트랙을 깔고 그 트랙을 따라 움직이는 카메라. Dolly Track 과 함께 쓰인다.    
**FreeLook Camera** : 오브젝트를 중심으로 원형의 링을 생성하여 그 구간 안에서 타깃을 관찰하는 카메라.    
**Mixing Camera** : Child Camera Weight 값에 따라 활성화/비활성화 해주는 카메라.    
**State-Driven Camera** : 타겟 에니메이션의 상태별로 활성화/비활성화 시킬 수 있는 카메라.    
**Target Group Camera** : 그룹으로 묶인 카메라들을 자동으로 계산해 한 화면에 보여주는 카메라.    
**Virtual Camera (가상 카메라)** : 가장 기본이 되는 카메라로, 유니티 카메라를 조정하듯 자유롭게 활용이 가능합니다.   
정도가 될 것 같습니다.    

기본적으로 모든 카메라는 Virtual Camera를 베이스로 깔고 추가적인 기능을 합니다.    
그럼 이제 어떻게 사용하는지에 대해서 알아보겠습니다.    

----

가장 먼저 Main Camera에 아래 이미지처럼 CinemachineBrain 컴포넌트를 달아줘야 합니다.    

![image](https://github.com/user-attachments/assets/5ac40b37-242c-434b-8334-ae50b1d6ace3)    

Virtual Camera를 조작하려는데 왜 Main Camera도 설정을 해줘야하는지 의문이 생깁니다.    
왜냐면 Virtual Camera는 이름처럼 '가상'의 카메라이기 때문입니다.    
그래서 실제로 카메라 역할을 해주는 Main Camera가 이러한 가상 카메라들의 뇌 역할을 해줘야 합니다. (CinemachineBrain인 이유)    

이제 가상 카메라를 조작해봅시다.    
위에서 말했듯이 하이라키에서 가상 카메라 한개를 만들어봅시다.    
카메라를 생성하면 아래와 같은 이미지처럼 나올겁니다.    

![image](https://github.com/user-attachments/assets/b3174e1f-ae67-4448-9c9f-28a7a29821fb)    
<img src='https://github.com/user-attachments/assets/b3174e1f-ae67-4448-9c9f-28a7a29821fb', width="500">    





