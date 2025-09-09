#### 샌드웜 움직임    
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

<br>
<br>    

----     

<br>    

아래는 움직임들을 구현하기 위한 코드와 설명입니다.    

순서대로 패턴 모션, 머리 공격(근접 공격), 숨거나 나오는 함수의 코드입니다.    
이는 고정된 상태에서 사용하는 패턴으로 Multi-Parent Constraint를 사용했습니다.    
모든 움직임은 유니티 에셋인 DOTween을 이용하여 구현했습니다.    
이는 더 부드럽고 상황에 맞게 다양하게 적용할 수 있는 현실감있는 움직임을 위함입니다.    

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
AttackMove함수에서는 패턴을 쓸 때 머리를 회전시키는 함수입니다.    
패턴 전, 패턴 중, 패턴 후 이렇게 3단계를 통해 샌드웜의 움직임을 묘사합니다.    

https://github.com/user-attachments/assets/39ac1cf1-a5d6-4301-a3d0-043dc630397d    

<br><br>

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
HeadAttackMove함수는 특정 위치로 머리를 찍는 움직임을 묘사하는 함수입니다.    
특징으로 패턴 전의 움직임을 머리가 아닌 몸통 중간 부위를 뒤로 당겨서 움직임의 반동을 주려는 시도를 했습니다.    
그리고 3개의 지점을 지정해 곡선 궤도로 부드럽게 움직이도록 구현했습니다.    

https://github.com/user-attachments/assets/ad0799ff-8923-4ecb-a577-6f9626fd7a17    

<br><br>

```csharp
public IEnumerator HideOrShow(bool hide, float duration, bool fixedDirection = false)
{
    isIdle = false;
    Vector3 targetPos = SceneContext.Character.transform.position;
    Vector3 dir = fixedDirection ? new Vector3(0, -1, 0) : (targetPos - root.transform.position).normalized;
    int length = sandwormBody.Count;
    
    if (hide)
    {
        float start = head.data.sourceObjects.GetWeight(0);
        var seq = DOTween.Sequence();
        seq.Append(DOTween.To(() => start, w =>
        {
            var s = head.data.sourceObjects;
            s.SetWeight(0, w);
            s.SetWeight(1, 1 - w);
            head.data.sourceObjects = s;
            start = w;
        }, 0, duration / 8).SetEase(Ease.OutQuad));
        
        seq.Append(bodyTip1.transform.DOMove(transform.position, duration / 8).SetEase(Ease.OutQuad));
        seq.Join(bodyTip2.transform.DOMove(transform.position, duration / 8).SetEase(Ease.OutQuad));

        seq.AppendInterval(duration / 8);
        
        for (int i = 0; i < length; i++)
        {
            int idx = length - 1 - i;
            seq.Append(sandwormBody[idx].DOFade(0, duration / 4 / length).SetEase(Ease.OutQuad));
            _hidden[idx] = true;
        }

        seq.AppendInterval(duration / 8 * 3);

        yield return seq.WaitForCompletion();
        moveTip.transform.position = transform.position;
        var s = head.data.sourceObjects;
        s.SetWeight(0, 1);
        s.SetWeight(1, 0);
        head.data.sourceObjects = s;
        yield return null;
    }
    else
    {
        if (!fixedDirection)
        {
            isIdle = true;
            FacePlayer(true);
            isIdle = false;
        }
        Vector3 midPos = (moveTip.transform.position + transform.TransformPoint(new Vector3(0, 2.4f, 0)) + dir * 1f) / 2 - dir;
        moveTip.transform.position = transform.position;
        var tween = moveTip.transform.DOPath(
            new[]
            {
                moveTip.transform.position,
                midPos,
                transform.TransformPoint(new Vector3(0, 2.4f, 0)) + dir * 1f
            },
            duration
            , PathType.CatmullRom
        );
        
        tween.SetEase(Ease.OutCubic)
            .OnUpdate(() =>
            {
                float progress = tween.ElapsedPercentage();
                
                for (int i = 0; i < length; i++)
                {
                    float threshold = (i + 1) / (float)length * 0.5f;
                    if (_hidden[i] && progress >= threshold)
                    {
                        sandwormBody[i].DOFade(1, 0.1f);
                        _hidden[i] = false;
                    }
                }
            });
        
        yield return tween.WaitForCompletion();
        
        isIdle = true;
    }
}
```
HideOrShow함수는 말 그대로 샌드웜이 땅에 숨거나 땅에서 튀어나오는 함수입니다.    
특이점으로는 땅에 숨는경우 (hide가 true일 때) 머리를 바닥쪽으로 이동시키는 방법이 아니라, 머리부분의 Source의 가중치를 0으로 만들어주는 방법으로 구현했습니다.    
이는 컴포넌트 특성상 머리가 움직이고 이후의 몸통이 따라오는 형식이기 때문에 머리를 움직이게되면 몸 뒤에서부터 땅으로 파고드는 느낌이 안 나기 때문입니다.    
또 땅에서 튀어나오는 코드에서는 나오기 전에 플레이어 방향쪽으로 몸체의 방향을 돌려주는(FacePlayer) 과정을 한번 거쳤습니다.    

https://github.com/user-attachments/assets/29e85a6c-a414-48e5-b874-5bbd067b71b1    



<br>
다음은 비고정된 상태로 Damped Transform을 사용한 부분입니다.    

```csharp
public IEnumerator BodyAttackMove(Vector3 targetPos, float speed)
{
    isIdle = true;
    FacePlayer(true);
    isIdle = false;
    
    int length = sandwormBody.Count;
    float dist = Vector3.Distance(root.transform.position, targetPos);
    float duration = dist / speed;
    
    moveTip.transform.position = transform.position;
    SetRigState(true);

    var attackTween = moveTip.transform.DOJump(targetPos, dist / 6, 1, duration)
        .SetEase(Ease.OutQuad);
    Sequence seq = null;
    attackTween
    .OnUpdate(() =>
    {
        float progress = attackTween.ElapsedPercentage();
        for (int i = 0; i < length; i++)
        {
            float threshold = (i + 1) / (float)length * 0.3f;
            if (_hidden[i] && progress >= threshold)
            {
                sandwormBody[i].DOFade(1, 0.3f / length).SetEase(Ease.Linear);
                _hidden[i] = false;
            }
        }
    })
    .OnComplete(() =>
    {
        seq = DOTween.Sequence();
        for (int i = 0; i < length; i++)
        {
            int index = i;
            seq.Append(
                sandwormBody[index]
                    .DOFade(0, 0.5f / length)
                    .SetEase(Ease.Linear)
                    .OnComplete(() => _hidden[index] = true)
            );
        }
    });

    yield return attackTween.WaitForCompletion();
    yield return seq.WaitForCompletion();
    yield return new WaitForSeconds(duration * 0.3f);
    
    SetRigState(false);
    transform.position = targetPos;
    root.transform.position = transform.TransformPoint(new Vector3(0, 0, 0));
    moveTip.transform.position = transform.TransformPoint(new Vector3(0, 0, 0));
    yield return null;
}
```
```csharp
private void SetRigState(bool damped)
{
    foreach (var s in sandwormBody)
    {
        s.GetComponent<MultiParentConstraint>().weight = damped ? 0f : 1f;
        s.GetComponent<DampedTransform>().weight = damped ? 1f : 0f;
    }
}
```
BodyAttackMove함수는 땅에 숨어있는 상태에서 시작해 인자로 받은 위치까지 샌드웜이 튀어나와 돌진하는 함수입니다.    
위에서와 마찬가지로 튀어나오기 전 플레이어 방향으로 바라보게 만들어줍니다.    
Damped transform으로 전환하는 것은 아래의 SetRigState함수를 이용해 단순히 가중치를 바꿔주는 것으로 구현했습니다.    

https://github.com/user-attachments/assets/abe7b8ab-bcd5-4c79-be05-2e8bdc1d3462    
