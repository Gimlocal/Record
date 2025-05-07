### 안개 시스템    
![image](https://github.com/user-attachments/assets/baf69096-beee-47af-9437-78b391321277)

먼저 Visual Effect Graph를 만듭니다.    
![image](https://github.com/user-attachments/assets/913f0987-a35b-448a-b014-1b0e7d02cb5a)    
사진과 같이 Visual Effect칸이 없으면, PackageManager에서 Visual Effect Graph를 설치하면 됩니다.    
생성 후 탭을 더블클릭해 열어보면 아래와 같은 이미지가 나오게 됩니다.    
![image](https://github.com/user-attachments/assets/256d8354-2a8f-4d4b-ae9d-4fd479183c83)    
하나씩 살펴보죠.    

![image](https://github.com/user-attachments/assets/493fcbeb-0530-45fa-8695-d778f3f3c8e9)    
먼저 Spawn부분입니다. Rate 옆의 오른쪽 파란색 점을 잡고 마우스로 끌면 이어지는 노드를 만들 수 있습니다.    
이름에서 알 수 있듯 소환하는 이펙트의 양을 결정하는 부분입니다.    
float 노드를 만들어서 안개의 양을 나타내는 FogAmount노드를 만들어보겠습니다. 저는 500정도로 했습니다.    
![image](https://github.com/user-attachments/assets/615ec5fe-4c4f-4753-9e91-a0c4af29271d)    

![image](https://github.com/user-attachments/assets/3bce9802-f136-4d35-8d3e-fed2c67ad879)    
다음은 Initialize Particle부분 입니다.    
말 그대로 이펙트(파티클)의 각종 설정을 할 수 있습니다. 속도, 주기, 위치, 크기를 설정할 수 있습니다.    
원하는대로 노드를 안쪽에서도 만들어서 설정하면 됩니다.    
보통 Set Position (Shape : AABox)를 추가해서 이펙트가 생성되는 공간을 추가합니다.    
![image](https://github.com/user-attachments/assets/c7d66180-cdbd-4536-81e8-cdea3b5c2de2)
![image](https://github.com/user-attachments/assets/8fab5e5d-9815-4f0e-ac63-3082fc8da53e)


여기까지 하시면 사실 안개가 생기는건 거의 완성입니다.    
하지만 안개 시스템에서 안개가 생기는것도 중요하지만 더 중요한것은 안개가 플레이어와 어떻게 상호작용되는 것 입니다.    
저는 플레이어 주위에서는 안개가 걷히는 효과를 넣어줬습니다.    
![image](https://github.com/user-attachments/assets/a797fca1-34ef-450d-b97e-4fedc776a17a)    
이 Update Particle부분이 그 효과를 더해줍니다.    
Collide with Sphere을 체크해주고, 아래 설정들을 바꿔주시면 됩니다.    
여기서 이 Collider가 안개를 제거해주는 물체라고 생각하시면 됩니다.    
그래서 이 Collider의 position을 노드로 뺀 후, 외부 스크립트에서 플레이어의 위치와 동일하게 맞춰주는 작업도 필요합니다.    
그 외에 이 Collider의 크기를 정하는 Radius, 안개의 양을 얼마나 줄일지 정하는 Lifetime Loss 등이 있습니다.    
입맛대로 설정해주시면 될거 같네요.    

![image](https://github.com/user-attachments/assets/25880434-6625-4cf0-bf76-f94fe73e1218)    
마지막인 Output Particle Quad로 이펙트(파티클)의 모양이나 크기변화, 색변화 등을 설정하는 부분입니다.    
저는 여기서 MainTexture을 default-particle로 설정해서 안개처럼 만들었습니다.    



끝으로 코드에서 어떻게 이 시스템에 접근하는지 간단하게 보여드리겠습니다.    
Hierarchy에서 Visual Effect를 만들면 위에서 만들었던 Visual Effect Graph를 넣을 수 있는 오브젝트를 만들 수 있습니다.    
![image](https://github.com/user-attachments/assets/39ea3496-6807-40c8-84c5-4ed7295c30e5)
![image](https://github.com/user-attachments/assets/eb948cc1-c242-4437-a011-877a0f4dd88d)    
이름을 취향껏 바꿔주고, Visual Effect 컴포넌트의 Asset Template에 아까 만든 Visual Effect Graph를 넣으면 됩니다.
이후 아래와 같은 스크립트를 하나 만들어서 넣어주면 됩니다.    

```csharp
using UnityEngine.VFX;
public class FogController : MonoBehaviour
{
    [SerializeField] private VisualEffect vfxRenderer;

    private void Start()
    {
        vfxRenderer.SetVector3("ColliderPos", PlayerCharacter.Instance.transform.position);
    }
}
```

이렇게 하면 간단하게 안개 시스템을 만들 수 있습니다.    
Visual Effect Graph를 이용하면 안개 말고도 눈보라, 사막의 모래폭풍, 비 등의 다양한 효과를 만들 수 있을겁니다.    
마지막으로 실제 사용 예시입니다.    
