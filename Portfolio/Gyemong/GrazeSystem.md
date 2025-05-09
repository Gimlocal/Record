### 스치기 시스템    
전투에 플레이어와 보스의 상호작용을 하는 느낌을 주고싶어서 추가하게된 시스템입니다.    
기본적으로 보스의 원거리 공격을 플레이어가 맞지않게 아슬아슬하게 피하면 스치기가 되는 시스템입니다.    
스치기가 성공하면 아래 영상처럼 스치기 범위가 플레이어 주변에 표시되고, 성공했다는 소리도 나옵니다.    
또한 스치기가 성공하면 특수 공격을 할 수 있는 게이지도 찹니다.    

<img src="https://github.com/user-attachments/assets/b990f4e4-a3de-4e27-b3eb-34bba0dc499c" width="500">    

 - #### 구현
아래는 구현을 위한 C#코드입니다.
```csharp
public class GrazeController : MonoBehaviour
{
    [SerializeField] private float maxAttackDistance = 1f;
    private List<Collider2D> activeColliders = new();
    private Dictionary<Collider2D, float> colliderDistanceMap = new();
    public Dictionary<Collider2D, bool> colliderAttackedMap = new();

    private void OnTriggerEnter2D(Collider2D collider)
    {
        if (collider.CompareTag("EnemyAttack"))
        {
            if (!activeColliders.Contains(collider))
            {
                activeColliders.Add(collider);
                colliderDistanceMap[collider] = maxAttackDistance;
                colliderAttackedMap[collider] = false;
                Debug.Log($"Collider entered: {collider.name}");
            }
        }
    }

    private void OnTriggerStay2D(Collider2D collider)
    {
        if (collider.CompareTag("EnemyAttack"))
        {
            float curDistance = Vector2.Distance(transform.position, collider.transform.position);

            if (colliderDistanceMap.ContainsKey(collider))
            {
                colliderDistanceMap[collider] = Mathf.Min(curDistance, colliderDistanceMap[collider]);
            }
        }
    }

    private void OnTriggerExit2D(Collider2D collider)
    {
        if (collider.CompareTag("EnemyAttack"))
        {
            if (activeColliders.Contains(collider) && !colliderAttackedMap[collider])
            {
                Grazed(collider);
                RemoveCollider(collider);
            }
            else
            {
                RemoveCollider(collider);
            }
        }
    }

    private void Grazed(Collider2D collider)
    {
        if (colliderDistanceMap.TryGetValue(collider, out float distance))
        {
            PlayerCharacter.Instance.GrazeIncreaseGauge(distance);
            GetComponentInChildren<GrazeOutlineController>().AppearAndFadeOut();
            Debug.Log($"Gauge Increased by {PlayerCharacter.Instance.gaugeIncreaseValue / distance} with ratio {distance}");
        }
    }

    private void RemoveCollider(Collider2D collider)
    {
        activeColliders.Remove(collider);
        colliderDistanceMap.Remove(collider);
        colliderAttackedMap.Remove(collider);
    }
}
```

코드 흐름은 다음과 같습니다.    
1. EnemyAttack 태그가 달린 적의 공격이 스치기 범위에 들어온다.
2. 범위내에 존재할 때, 플레이어와의 최소거리를 계속 측정한다. (늘어나는 게이지의 양을 정하기 위해서. 아슬아슬하게 피할수록 게이지 증가량 늘어남)
3. 적의 공격이 범위에서 벗어나면 플레이어가 맞았는지를 확인해서 안맞았으면 스치기가 성공한다. (Graze함수 실행)

그리고 각 단계에서 공격에 대한 정보를 담을 수 있는 Map을 만들어서 그곳에서 추가하고 빼는 방식으로 구현했습니다.    
조금 더 자세하게는 각 공격이 Key가되는 Map을 3개 만들어서 각각의 Map을 스치기 범위에 속하는지, 최소거리 측정, 그리고 공격을 받았는지 확인하는 용도로 이용했습니다.    

 - #### 아쉬웠던 점    
한 가지 아쉬웠던 점은 모든 공격이 플레이어를 향해 날아오지는 않는다는 것 이었습니다.    
예를 들어서 이 게임에 골렘이 돌을 집어던져서 캐릭터 머리위에서 떨어지도록 하는 공격이 있는데, 이 공격은 플레이어 위에서 떨어지는 공격이기 때문에 최소거리를 측정하기가 조금 애매했습니다.
그래서 플레이어가 떨어진 돌에 접근해서 게이지 상승량을 높일 수 있게 하는 편법도 존재했습니다.
그래서 각 공격별로 예외처리를 해서 화살처럼 플레이어의 위치로 날아오는 공격에 대해서는 최소거리를 측정해 적용하고, 단순히 플레이어의 위치에 떨어지는 공격에 대해서는 거리를 기본값이 적용되게 했습니다.


