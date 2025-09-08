### 샌드웜

#### 1. 샌드웜 움직임    
기존의 보스와는 다르게 샌드웜은 여러개의 관절로 이루어져 있어서 이 관절들을 서로서로 이어주고 마치 뱀 처럼 움직이는 듯한 효과를 주기 위해 추가적인 작업이 필요했습니다.    
여러 오브젝트를 묶어줄 방법을 찾는 와중에 애니메이션 리깅이라는 패키지를 발견했습니다.    


- #### 애니메이션 리깅    
  애니메이션 리깅은 간단하게 말하자면, 애니메이션의 뼈대를 설정하고 조작하는 과정입니다.    
  즉, 어떤 오브젝트에 뼈대를 심어서 그 뼈대를 토대로 오브젝트들을 붙여서 실제로 그 사물이 움직이는 듯한 현실감을 준다는 거죠.    
  이 기능은 유니티에 내장되어 있으며, 패키지 매니저를 통해 다운받아 사용할 수 있습니다.

이 애니메이션 리깅에도 다양한 기능들이 있습니다.    

- Chain IK(Inverse Kinematic) Constraint
  - Root를 고정시키고 Tip을 움직여서 마치 기계팔을 움직이듯이 물체들을 연결해서 이용할 수 있음.
  - https://docs.unity3d.com/kr/Packages/com.unity.animation.rigging@0.2/manual/constraints/ChainIKConstraint.html
- Damped Transform
  - Tip을 움직이면 뒤에 연결된 오브젝트들이 감쇠된(damped) 움직임, 회전을 받아 움직임.
  - https://docs.unity3d.com/kr/Packages/com.unity.animation.rigging@1.1/manual/constraints/DampedTransform.html

제가 구현하려고 하는 샌드웜은 땅에 고정된 상태로 머리만 움직이며 패턴을 쓰는 방식과 땅에서 튀어나와서 특정 위치로 돌진하는 비고정된 상태 2가지가 필요했습니다.    
그래서 무조건 Root가 고정이 되는 Chain IK Constraint, 무조건 몸체들이 Tip을 따라오는 Damped Transform을 사용할 수 없었습니다.    
두 가지를 다 사용해서 중간중간에 바꿔주면 되지 않을까 싶었는데, 런타임중에 애니메이션 Rig를 변경하는것이 불가능했기 때문에 어쩔 수 없이 다른 방법을 찾아야 했습니다.    

그래서 조금 가볍게 사용할 수 있는 방법인 Multi-Parent Constraint를 사용했습니다.    

 - Multi-Parent Constraint
   - 말 그대로 여러개의 부모(Source)를 두어 이 부모의 움직임을 가중치에 따라 움직이게 설정해주는 컴포넌트.
  
머리부터 말단 몸통까지 각자의 앞/뒤에 있는 몸통(머리와 마지막 몸통은 각각을 끌어주는 Tip과 이전/이후 몸통으로 설정)으로 Source로 두어 설정해주었습니다.    

<img src="https://github.com/user-attachments/assets/d431069a-0d56-47b1-aac1-75d1aadd3cbb" />

사진과 같이 Body1의 경우 양옆의 Head와 Body2를 Source로 두어 그 두개의 오브젝트의 움직임을 각각 50%만큼 영향을 받도록 설정했습니다.    
  

