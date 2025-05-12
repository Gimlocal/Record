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

<img src="https://github.com/user-attachments/assets/b3174e1f-ae67-4448-9c9f-28a7a29821fb" width="800">    

Main Camera옆에 빨간 카메라 표시가 보이는데, 가상 카메라와 잘 연결됐다는 표시입니다.    
그렇기 때문에 이제 가상 카메라에서 카메라 설정을 바꾸면 그게 Main Camera에서도 적용이 된다는 뜻입니다.    
예를들어 가상 카메라의 Lens Ortho size를 조절하면 Main Camera의 Projection 컴포넌트의 Size를 조절하는 것과 같은 효과를 보입니다.    
그리고 이제 카메라의 설정들은 가상 카메라를 따라가기 때문에, Main Camera의 몇몇 설정들은 변경되지 않을겁니다.    

이제 가상 카메라의 다양한 기능에 대해서 알아보겠습니다.    
알아볼 기능은 아래와 같습니다.    

1. 오브젝트 추적
2. 카메라의 움직임 제한
3. 카메라 전환
4. 카메라 흔들림림
 
물론 위의 기능들이 가상 카메라의 모든 기능은 아니며, 게임에 사용된 기능들입니다.    
하나씩 살펴보겠습니다.    


#### 1. 특정 오브젝트 추적    

![오브젝트-추적](https://github.com/user-attachments/assets/92fd5755-ced8-4563-99eb-9cd1be88ec49)

가장 기본적인 기능입니다.    
보통 플레이어를 기준으로 카메라가 움직이니까 플레이어 기준으로 설명을 드리겠습니다.    
위의 가상 카메라 오브제트의 CinemachineVirtualCamera 컴포넌트를 보면 Follow라는 항목이 있습니다.    
이곳에 플레이어 오브젝트를 넣어주면 됩니다. 그러면 이제 카메라가 플레이어를 따라갈 겁니다.    
밑의 Aim은 오브젝트가 바라보는 방향으로 카메라를 돌리고 싶을 때 사용하는 기능입니다. 보통 3D게임에서 많이 쓰죠.    
Follow에 오브젝트를 넣어주면 이제 Body부분을 사용할 수 있는데, 여기에도 여러가지 방식이 있습니다.    

![image](https://github.com/user-attachments/assets/6411739d-8549-42d9-950e-d518d9ab898b)    

저희가 쓸 방식은 Framing Transposer입니다.    
Framing Trasposer은 대상이 화면에서 일정한 위치를 유지하도록 합니다. 즉 카메라가 어떻게 오브젝트를 따라갈 지 상세하게 설정할 수 있습니다.     
이 설정으로 바꾸면 밑에 추가 설정들이 많이 생깁니다.    

![image](https://github.com/user-attachments/assets/7c2cbcdd-867b-4d7c-8009-db2ac3156946)    

여기서 저희가 사용할 내용은 Dead Zone, Soft Zone 입니다.    

![image](https://github.com/user-attachments/assets/2a7735d3-7639-4ea0-9b2f-666073381914)     

플레이어를 자세히 보시면 노란색 점이 보이실 텐데 이 점이 영역들과 위치에 따라 상호작용합니다. (점은 Follow 오브젝트에 생김. 이하 플레이어)    
여기서 Dead Zone은 점이 존재하는 색이 없는 공간입니다.    
이 공간에서는 플레이어가 움직여도 카메라가 따라오지 않게 됩니다.    
Dead Zone 너머에 있는 푸른 공간이 Soft Zone입니다.    
이 공간에서는 플레이어의 움직임에 따라 카메라가 플레이어를 따라갑니다.    
마지막으로 Soft Zone 너머에 있는 보라색 영역이 있습니다.    
이 영역에는 공식 명칭이 없고 Hard zone, out of zone 등과 같은 명칭으로 불립니다.    
이 영역은 플레이어가 절대로 존재할 수 없는 영역입니다.    
울타리로 막아서 못 나가게 하는 것과 비슷하죠.    

이러한 설정들을 Body영역에서 자유롭게 설정하면 됩니다.    

----

#### 2. 카메라의 움직임 제한    

![카메라-움직임-제한](https://github.com/user-attachments/assets/fbd397d6-26a7-4a07-a1b7-0c13e2307912)

플레이어에게는 보통 맵을 벗어나지 못하도록 벽으로 막아둡니다.    
카메라도 마찬가지입니다. 플레이어가 벗어나지 못하는 곳에서는 카메라도 못 넘어가게 막아서 조금 더 현실성을 추가하곤 하죠.    
사용법은 그리 어렵진 않습니다.    

![image](https://github.com/user-attachments/assets/efb990e5-8a7e-443d-9ce5-36c28eb14312)    

위의 이미지처럼 CinemachineVirtualCamera 컴포넌트의 맨 아래쪽을 보면 Add Extension이 존재합니다.    
이 항목은 Cinemachine에 존재하는 다양한 기능을 추가할 수 있는 기능입니다.    
저희는 2D게임이기에 CinemachineConfiner2D를 추가합니다.    
이 기능을 추가하면 컴포넌트가 새로 생기고, 여기에 Collider를 넣을 수 있게됩니다.    
이 부분에는 Polygon Collider나 Edge Collider밖에 안 들어가기 때문에 둘 중 하나를 골라서 넣어주시면 됩니다.    
Collider를 넣어주기만 하면 이제 카메라는 플레이어를 따라가면서 이 Collider에 막히게 됩니다.    
즉 카메라에게만 적용되는 벽을 넣어주는거죠.    

----

#### 3. 카메라 전환    

![카메라-전환](https://github.com/user-attachments/assets/f562bb57-a3f8-4db0-992e-a32b187b91e8)

특정 영역을 벗어나면 막혀있던 카메라가 다음 영역으로 넘어가는 듯한 느낌을 주는 효과입니다.    
사실 카메라가 넘어가는 것은 아니고, 여러대의 카메라를 각 영역에 배치해놓고 플레이어가 경계를 넘어가면 넘어간 영역에 있는 카메라가 플레이어를 비춰주는 원리입니다.    
그렇기 때문에 각 영역마다 카메라를 넣어주고, 각 카메라마다 Collider를 달아주고(이때 Collider끼리 겹치면 안됨), 경계가 넘어가면 카메라가 전환 가능하게 해주는 등 해야할 일이 좀 많습니다.    

그러면 여기서 궁금증이 생기죠. 카메라가 여러대고 이 카메라가 전부 플레이어를 Follow하고 있을텐데, Main Camera에서 어떻게 하나의 카메라를 지정해서 보여주지?    
답은 간단합니다. 카메라마다 우선순위를 설정해주면 됩니다.    

![image](https://github.com/user-attachments/assets/bc981e7d-b4aa-44e7-b8c9-5c9d1db7cbd7)    

컴포넌트에 존재하듯이 Priority를 설정해주면, 이 우선순위가 제일 높은 카메라를 Main Camera가 잡아줍니다.    
그리고 저희는 코드를 통해서 플레이어가 경계를 넘어가면 해당 영역의 카메라의 우선순위를 높여주는 형식으로 진핼할 예정입니다.    

```csharp
public class CameraSetter : MonoBehaviour
{
   private void OnTriggerEnter2D(Collider2D other)
   {
       if (other.CompareTag("Player"))
       {
           CameraManager.Instance.ChangeCamera(transform.GetComponentInChildren<CinemachineVirtualCamera>());
       }
   }
}

public class CameraManager : SingletonObject<CameraManager>
{
     private CinemachineVirtualCamera currentCam;
     
     public void ChangeCamera(CinemachineVirtualCamera newCam)
     {
       if (currentCam != null)
       {
           currentCam.Priority = 0;
       }
       newCam.Priority = 10;
       currentCam = newCam;
     }
}
```

코드에서 알 수 있듯이 Collider을 가지고있는 오브젝트에 플레이어가 닿으면 currentCam의 Priority를 조정해주는 식으로 구현했습니다.    

----

#### 4. 카메라 흔들림    

![카메라-흔들림](https://github.com/user-attachments/assets/468cd996-bcaa-46b4-a31e-c151a426a216)    

이 기능도 게임에서 빈번히 사용되죠. 플레이어 피격, 강력한 공격, 환경요소 등과 같은 많은 부분에서 카메라 흔들림이 사용되죠.    
어떻게 사용되는지 알아보겠습니다.    

![image](https://github.com/user-attachments/assets/3135420d-3642-4d27-bcb5-2d06eec8f928)    

이미지처럼 두 개의 컴포넌트가 필요합니다.    
컴포넌트 앞에 모양을 보면 아시겠지만, Impulse Listener은 Add Extension에서 Impulse Source는 Add Component에서 추가해줍니다.    
이름에서 어느정도 유추할 수 있듯이 Impulse Source에서 실제로 충격(impulse)를 만들고 Listener에서 이것을 감지해 카메라에 넘겨줍니다.    
그리고 흔들림은 스크립트를 통해서 생성할 수 있습니다.    

```csharp
public void CameraShake(float force)
{
  CinemachineImpulseSource impulseSource = currentCam.GetComponent<CinemachineImpulseSource>();

  for (int i = 0; i < 5; i++)
  {
      Vector3 randomShake = new Vector3(Random.Range(-1f, 1f), Random.Range(-1f, 1f), 0).normalized;
      impulseSource.GenerateImpulse(randomShake * force);
  }
}
```

현재 우선순위가 높은 카메라 currentCam의 Impulse Source 컴포넌트에 접근해서 GenerateImpulse 함수를 실행해주면 됩니다.    
이 함수는 Vector3를 매개변수로 받고 이 방향으로 카메라를 흔듭니다.    
흔들림에 대한 설정은 컴포넌트의 Impulse Shape에서 정할 수 있습니다.    
저는 순간적인 강력한 흔들림을 원해서 Explode로 설정했습니다.    
또한 GenerateImpulse는 변수로 받은 방향으로만 카메라를 한번 흔들기 때문에, 랜덤 방향으로 여러번 카메라를 흔들어주기 위해서 for문을 돌렸습니다.    

----

이렇게 Cinemachine을 이용한 카메라 설정을 알아봤습니다.    
여기에 나온 기능 말고도 다양한 기능들이 있어서 추후 더 많은 기능을 사용해볼 예정입니다.    
기회가 된다면 이렇게 기능을 쓰기만 하는게 아니라 직접 기능을 구현해보는것도 좋은 경험이 될 것 같습니다.
