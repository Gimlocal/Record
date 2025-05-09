### 분열 슬라임    
슬라임 정원에 존재하는 미니보스 느낌의 분열 슬라임입니다.    
잡몹은 아니고 보스도 아닌 그 사이에 있는 몹이라서 살짝 간단하게 구현해봤습니다.    
아래는 영상입니다.    

<img src="https://github.com/user-attachments/assets/a406d3a2-d5f0-4350-af00-ea97e9d4387b" width="500">    


단계별로 하나씩 설명해보겠습니다.    
영상 앞부분에 잠깐 지나가긴 했는데, 슬라임이 합쳐지고 분열하는 큰 슬라임이 나오는 그런 연출을 만들어봤습니다.    
먼저 **슬라임이 합쳐지는 부분**부터 보겠습니다.    

아래는 코드입니다.     
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
사실 이 합쳐지는 연출은 아주 간단합니다.    
slime 배열에 합쳐질 때 사용되는 작은 슬라임들을 원하는 개수만큼 넣어주고, 중심과 모체가 되는 작은 분열 슬라임 targetSlime을 배치해놓습니다.    
이후 targetSlime을 중심으로 크기가 반지름이 4인 원안의 무작위 위치에 하나씩 작은 슬라임을 보이게 합니다.    
현재는 SetActive로 존재하는 작은 슬라임을 껐다 켰다 하는 방식으로 했는데, 슬라임의 수가 적어서 Instantiate를 이용해서 소환하는 형식으로 해도 좋을거 같네요.    
이후 targetSlime과의 방향을 계산해서 각각의 작은 슬라임들에게 MoveSlimeToTarget 코루틴을 적용시켜주면 영상에서 보이는 것처럼 합쳐지는 느낌을 줄 수 있습니다.    
또 합쳐질때는 모체 슬라임의 Scale을 늘어나게해서 크기를 키워줍니다. 여기서 디테일하게 보여주기 위해 Scale이 커졌다가 살짝 줄어드는 느낌을 추가해봤습니다.    

다음은 **분열 슬라임**의 코드입니다.    
```cpp
public class DivisionSlime : SlimeBase
{
    private const float DIVIDE_RATIO = 0.6f;
    private int _divisionLevel = 0;
    private int _maxDivisionLevel = 2;
    
    protected override void Start()
    {
        Initialize();
        DivisionSlimeManager.Instance.RegisterSlime(this);
    }

    protected override void Initialize()
    {
        maxHp = 10f;
        currentHp = maxHp;
        speed = 2f;
        
        _detector = SimplePlayerDetector.Create(this);
        _pathFinder = new SimplePathFinder();
        _slimeAnimator = SlimeAnimator.Create(gameObject, sprites);
        
        MeleeAttackRange = 3f;
        RangedAttackRange = 10f;
        detectionRange = 20f;

        damage = 10f;
    }

    public override void StartMob()
    {
        StartCoroutine(FaceToPlayer());
        ChangeState();
    }

    public override void OnAttacked(float damage)
    {
        base.OnAttacked(damage);
        if (currentState is not SlimeDieState)
        {
            StopCoroutine(_currentStateCoroutine);
            StartCoroutine(GetComponent<AirborneController>().AirborneTo(transform.position - DirectionToPlayer * MeleeAttackRange));
            ChangeState();
        }
    }

    public class SlimeRangedAttackState : RangedAttackState { }
    public class SlimeMeleeAttackState : MeleeAttackState { }
    
    public class DashAttackState : SlimeState
    {
        private DivisionSlime DivisionSlime => mob as DivisionSlime;
        public override int GetWeight()
        {
            return (DivisionSlime.DistanceToPlayer > DivisionSlime.MeleeAttackRange &&
                    DivisionSlime.DistanceToPlayer < DivisionSlime.RangedAttackRange) ? 5 : 0;
        }

        public override IEnumerator StateCoroutine()
        {
            DivisionSlime._slimeAnimator.AsyncPlay(SlimeAnimator.AnimationType.MeleeAttack, true);
            float dashDistance = 1.5f;
            Vector3 dashTargetPosition = PlayerCharacter.Instance.transform.position + DivisionSlime.DirectionToPlayer * dashDistance;
            yield return new WaitForSeconds(2 * SlimeAnimator.AnimationDeltaTime);
            DivisionSlime.transform.DOMove(dashTargetPosition, 0.3f).SetEase(Ease.OutQuad);
            yield return new WaitForSeconds(SlimeAnimator.AnimationDeltaTime);
            DivisionSlime._slimeAnimator.AsyncPlay(SlimeAnimator.AnimationType.Idle, true);
            yield return new WaitForSeconds(1);
            DivisionSlime.ChangeState();
        }
    }

    public class DieState : SlimeDieState
    {
        private DivisionSlime DivisionSlime => mob as DivisionSlime;
        public DieState() { }
        public DieState(StateMachineMob mob)
        {
            this.mob = mob;
        }
        public override int GetWeight()
        {
            return 0;
        }
        public override IEnumerator StateCoroutine()
        {
            if (DivisionSlime._divisionLevel < DivisionSlime._maxDivisionLevel)
            {
                DivisionSlime.Divide();
            }
            else
            {
                DivisionSlime._slimeAnimator.AsyncPlay(SlimeAnimator.AnimationType.Die);
                yield return new WaitForSeconds(SlimeAnimator.AnimationDeltaTime);
            }
            Destroy(DivisionSlime.gameObject);
        }
    }

    protected override void OnDead()
    {
        ChangeState(new DieState(this));
        DivisionSlimeManager.Instance.UnregisterSlime(this);
    }

    public class MoveState : SlimeMoveState { }

    private void Divide()
    {
        for (int i = 0; i < 3; i++)
        {
            Vector3 spawnPosition = transform.position;
            int attempts = 10;
            do
            {
                Vector3 candidatePosition = transform.position + Random.insideUnitSphere;
                candidatePosition.z = 0f;
                var hit = Physics2D.Raycast(transform.position, candidatePosition - transform.position, Vector3.Distance(transform.position, candidatePosition), LayerMask.GetMask("Wall"));
                if (hit.collider == null)
                {
                    spawnPosition = candidatePosition;
                    break;
                }
            } while (attempts-- > 0);
            
            GameObject newSlime = Instantiate(gameObject, transform.position, Quaternion.identity);
            newSlime.transform.localScale = transform.localScale * DIVIDE_RATIO;
            
            newSlime.transform.DOJump(spawnPosition, 1f, 1, 0.5f).SetEase(Ease.OutQuad);

            DivisionSlime slimeComponent = newSlime.GetComponent<DivisionSlime>();
            
            slimeComponent._slimeAnimator = SlimeAnimator.Create(slimeComponent.gameObject, sprites);
            slimeComponent.damage *= DIVIDE_RATIO;
            slimeComponent.MeleeAttackRange *= DIVIDE_RATIO;
            slimeComponent.RangedAttackRange *= DIVIDE_RATIO;
            slimeComponent._divisionLevel = _divisionLevel + 1;
            slimeComponent.ChangeState(new SlimeMoveState(slimeComponent));
            
            DivisionSlimeManager.Instance.RegisterSlime(slimeComponent);
        }
    }
}
```

