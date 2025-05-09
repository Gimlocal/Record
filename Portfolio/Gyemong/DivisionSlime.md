### 분열 슬라임    
슬라임 정원에 존재하는 미니보스 느낌의 분열 슬라임입니다.    
잡몹은 아니고 보스도 아닌 그 사이에 있는 몹이라서 살짝 간단하게 구현해봤습니다.    
아래는 영상입니다.    

<img src="https://github.com/user-attachments/assets/f781c6e2-10ef-4d2c-9c3a-864122af6abf" width="500">    

먼저 코드부터 살펴보겠습니다.    
```cpp
public class SlimeEvents : EventScene
{
    [SerializeField] private GameObject targetSlime;
    [SerializeField] private GameObject[] slimes;

    public override IEnumerator Execute(EventObject eventObject = null)
    {
        for (int i = 0;i < slimes.Length;i++)
        {
            GameObject randomSlime = slimes[i];
            randomSlime.SetActive(true);
            Vector2 randomDirection = Random.insideUnitCircle.normalized * 4f;
            Vector3 randomPosition = targetSlime.transform.position + (Vector3)randomDirection;
            randomSlime.transform.position = randomPosition;

            eventObject.StartCoroutine(MoveSlimeToTarget(randomSlime));

            float randomTime = Random.Range(0.5f, 1f);
            yield return new WaitForSeconds(randomTime);
        }

        yield return new WaitForSeconds(3f);
        targetSlime.GetComponent<DivisionSlime>().StartMob();
        // targetSlime.AddComponent<DivisionSlime>();
    }

    private IEnumerator MoveSlimeToTarget(GameObject slime)
    {
        Vector3 startPosition = slime.transform.position;
        Vector3 targetPosition = targetSlime.transform.position;
        float duration = Random.Range(1.5f, 2.5f);
        float elapsedTime = 0f;
        float delay = 0.01f;
        Vector3 scale = slime.transform.localScale;
        slime.transform.localScale = startPosition.x < targetPosition.x ? scale : new Vector3(-scale.x, scale.y, scale.z);
        slime.SetActive(true);

        while (elapsedTime < duration)
        {
            elapsedTime += delay;
            float t = elapsedTime / duration;
            slime.transform.position = Vector3.Lerp(startPosition, targetPosition, t);
            yield return new WaitForSeconds(delay);
        }
        slime.SetActive(false);

        targetSlime.transform.localScale *= 1.25f;
        yield return new WaitForSeconds(0.2f);
        targetSlime.transform.localScale *= 0.92f;
    }
}
```
