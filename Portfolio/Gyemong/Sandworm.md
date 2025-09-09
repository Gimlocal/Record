### 샌드웜

#### 1. 샌드웜 움직임    
기존의 보스와는 다르게 샌드웜은 여러개의 관절로 이루어져 있어서 이 관절들을 서로서로 이어주고 마치 뱀 처럼 움직이는 듯한 효과를 주기 위해 추가적인 작업이 필요했습니다.    
여러 오브젝트를 묶어줄 방법을 찾는 와중에 애니메이션 리깅이라는 패키지를 발견했습니다.    


- #### 애니메이션 리깅    
  애니메이션 리깅은 간단하게 말하자면, 애니메이션의 뼈대를 설정하고 조작하는 과정입니다.    
  즉, 어떤 오브젝트에 뼈대를 심어서 그 뼈대를 토대로 오브젝트들을 붙여서 실제로 그 사물이 움직이는 듯한 현실감을 준다는 거죠.    
  이 기능은 유니티에 내장되어 있으며, 패키지 매니저를 통해 다운받아 사용할 수 있습니다.

이 애니메이션 리깅에도 다양한 기능들이 있습니다.    
아래는 제가 처음에 사용하려고 했던 기능입니다.    

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

https://github.com/user-attachments/assets/0b3fdfe5-2455-4158-9f2c-1915d39ff82c    

위 영상이 고정된 상태인 샌드웜이고, 아래 영상이 비고정된 상태(돌진)인 샌드웜 입니다.    

https://github.com/user-attachments/assets/abe7b8ab-bcd5-4c79-be05-2e8bdc1d3462    

아래는 이 움직임들을 구현하기 위한 코드들 입니다.    
먼저 고정된 상태에서의 패턴과 패턴 모션을 만들어주는 코드입니다
```csharp
public IEnumerator AttackMove(bool dir, float upAngle, float upTime, float downAngle, float downTime, float recoverTime, float recoverAngle = 0f)
{
    var seq = DOTween.Sequence();

    seq.Append(headRotateTip.transform.DORotate
            (new Vector3(0, 0, dir ? upAngle : -upAngle), 
                upTime)
        .SetEase(Ease.OutSine));
    seq.Append(headRotateTip.transform.DORotate
            (new Vector3(0, 0, dir ? downAngle : -downAngle), 
                downTime)
        .SetEase(Ease.InQuad));
    seq.Append(headRotateTip.transform.DORotate(new Vector3(0, 0, recoverAngle), recoverTime)
        .SetEase(Ease.Linear));


    yield return seq.WaitForCompletion();
}
```
```csharp
public IEnumerator HeadAttackMove(Vector3 dest, float dur, float preDelay = 0.5f, float postDelay = 0.1f,
            float backDelay = 1f)
{
    Vector3 originalBodyPos = bodyTip1.transform.position;
    Vector3 originalHeadPos = moveTip.transform.position;
    
    Vector3 rootDir = (dest - root.transform.position).normalized;
    float rootDist = Vector3.Magnitude(dest - root.transform.position);
    float angle = dest.x > originalBodyPos.x ? -bodyRotateValue : bodyRotateValue;

    Sequence seq = DOTween.Sequence();
    seq.Append(bodyTip1.transform.DOMove(originalBodyPos - rootDir / 2, preDelay).SetEase(Ease.Linear));
    seq.Join(moveTip.transform.DORotate(new Vector3(0,0, angle), preDelay).SetEase(Ease.Linear));

    Vector3 startPos = moveTip.transform.position;
    Vector3 endPos = dest;
    float dist = Vector3.Distance(startPos, endPos);
    Vector3 midPos = (startPos + endPos) / 2 + Vector3.up * dist / 4;
    
    seq.Append(bodyTip1.transform.DOMove(originalBodyPos, dur).SetEase(Ease.OutCubic));
    seq.Join(moveTip.transform.DOPath(
        new[] { startPos, midPos, endPos },
        dur, 
        PathType.CatmullRom
    ).SetEase(Ease.OutCubic));
    seq.Join(moveTip.transform.DORotate(new Vector3(0,0,-angle), dur).SetEase(Ease.OutCubic));

    seq.AppendInterval(postDelay);
    
    seq.Append(moveTip.transform.DOMove(originalHeadPos, backDelay).SetEase(Ease.InOutSine));
    seq.Join(moveTip.transform.DORotate(Vector3.zero, backDelay).SetEase(Ease.InOutSine));
    
    yield return seq.WaitForCompletion();
}
```
순서대로 패턴 모션, 머리 공격(근접 공격) 코드입니다.

#### 2. 샌드웜 패턴    
샌드웜 패턴은 총 5가지 입니다.    
다음과 같이 독, 레이저, 땅에서 튀어나오기, 땅에서 튀어나와 돌진, 근접공격으로 구현되어있습니다.    


