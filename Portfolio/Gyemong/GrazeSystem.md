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

그리고 각 단계에서 정보를 확인할 수 있도록 각 공격에 대한 정보를 담을 수 있는 Map을 만들어서 그곳에서 추가하고 빼는 방식으로 구현했습니다.    

- 아쉬웠던 점
한 가지 아쉬웠던 점은 모든 공격이 플레이어를 향해 날아오지는 않는다는 것 이었습니다.
예를 들어서 이 게임에

