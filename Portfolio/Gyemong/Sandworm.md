### 샌드웜

#### 1. 샌드웜 움직임    
기존의 보스와는 다르게 샌드웜은 여러개의 관절로 이루어져 있어서 이 관절들을 서로서로 이어주고 마치 뱀 처럼 움직이는 듯한 효과를 주기 위해 추가적인 작업이 필요했습니다.    
여러 오브젝트를 묶어줄 방법을 찾는 와중에 애니메이션 리깅이라는 패키지를 발견했습니다.    


- #### 애니메이션 리깅    
  애니메이션 리깅은 간단하게 말하자면, 애니메이션의 뼈대를 설정하고 조작하는 과정입니다.    
  즉, 어떤 오브젝트에 뼈대를 심어서 그 뼈대를 토대로 오브젝트들을 붙여서 실제로 그 사물이 움직이는 듯한 현실감을 준다는 거죠.    
  이 기능은 유니티에 내장되어 있으며, 패키지 매니저를 통해 다운받아 사용할 수 있습니다.

이 애니메이션 리깅에도 다양한 기능들이 있습니다.    
아래는 제가 처음에 사용하려고 했던 기능 입니다.    

- Chain IK(Inverse Kinematic) Constraint
  - Root를 고정시키고 Tip을 움직여서 마치 기계팔을 움직이듯이 물체들을 연결해서 이용할 수 있음.
  - https://docs.unity3d.com/Packages/com.unity.animation.rigging@0.2/manual/constraints/ChainIKConstraint.html

구현하려고 했던 부분 중 샌드웜이 땅에 고정된 상태에서 패턴을 사용하는 부분이 있는데, 이 때 머리의 움직임에 따라 몸이 늘어났다 줄었다 하는 움직임을 보여주고 싶었기 때문에 길이가 고정되는 Chain IK Constraint 기능은 사용할 수 없었습니다.    
대신에 Multi-Parent Constraint를 사용했습니다.    
또 특정 위치로 튀어나가서 돌진하는 패턴을 위해 Damped Transform 기능도 사용했습니다.    

 - Multi-Parent Constraint
   - 말 그대로 여러개의 부모(Source)를 두어 이 부모의 움직임을 가중치에 따라 움직이게 설정해주는 컴포넌트.
   - https://docs.unity3d.com/Packages/com.unity.animation.rigging@1.1/manual/constraints/MultiParentConstraint.html
 - Damped Transform
   - Tip을 움직이면 뒤에 연결된 오브젝트들이 감쇠된(damped) 움직임, 회전을 받아 움직임.
   - https://docs.unity3d.com/Packages/com.unity.animation.rigging@1.1/manual/constraints/DampedTransform.html
  
머리부터 말단 몸통까지 각자의 앞/뒤에 있는 몸통(머리와 마지막 몸통은 각각을 끌어주는 Tip과 이전/이후 몸통으로 설정)으로 Source로 두어 설정해주었습니다.    

<img src="https://github.com/user-attachments/assets/bbbae2ea-4a85-4d63-a6d4-63e0f39bf85b" />    


위의 사진과 같이 Multi-Parent Constraint와 Damped Trasform을 둘 다 적용시켜놓고, 가중치를 이용해서 둘 중 하나를 골라서 사용하는 방식으로 했습니다.    
고정된 상태는 Mulit-Parent Constraint, 비고정된 상태는 Damped Transform을 이용해서 구현했습니다.    
초기 상태는 고정된 상태라 Damped Transform의 가중치(Weight)를 0, Multi-Parent Constraint의 가중치를 1로 설정해줬습니다.

중간에 있는 MoveBody같은 경우는 아래 사진과 같이 샌드웜의 휘어진 몸을 보여주기 위해서 중간중간 잡아 끌어주는 몸체를 2개 넣어줬습니다.    
<img src="https://github.com/user-attachments/assets/9c42380d-d89f-4570-95b6-a4925ee561e8" />    



  

