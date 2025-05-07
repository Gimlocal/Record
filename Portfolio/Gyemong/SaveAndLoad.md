### 세이브 및 로드 구현하기   

- #### 구현
#### 기본적인 로직은 직렬화 가능한 형태로 데이터를 만든 후, JSON 파일로 변환해서 특정 경로에 저장하는 방식입니다.    

```csharp
[System.Serializable]
public class PlayerData
{
    public bool isFirst = true;
    public string sceneName = "TutorialScene";
    public Vector3 playerPosition = new(4.93f, -1.72f, 0);
    public Vector2 playerDirection = new(1, 0);
}
```
예시로 로드될 때 사용되는 플레이어의 몇몇 정보를 담은 데이터를 직렬화 가능한 형태로 만든 코드입니다.    

```csharp
public void SaveSection<T>(T sectionData, string fileName) where T : class
{
    string json = JsonUtility.ToJson(sectionData, true); // JSON으로 직렬화
    string filePath = Path.Combine(savePath, fileName + ".json");

    // 파일 저장
    File.WriteAllText(filePath, json);

    Debug.Log($"{fileName} saved at {savePath}");
}

public T LoadSection<T>(string fileName) where T : class, new()
{
    string filePath = Path.Combine(savePath, fileName + ".json");
    if (File.Exists(filePath))
    {
        string json = File.ReadAllText(filePath); // JSON 파일 읽기
        return JsonUtility.FromJson<T>(json); // JSON에서 객체로 역직렬화
    }
    else
    {
        Debug.LogWarning($"{fileName} not found, returning new instance.");
        return new T(); // 파일이 없으면 새 인스턴스 반환
    }
}
```
데이터를 저장하는 SaveSection과 LoadSection 함수입니다.    
Section T에는 위의 데이터들의 클래스를 사용합니다. 또, Load시 저장된 파일이 없으면 초기값을 Load해주는 부분도 존재합니다.

```csharp
public class SaveDataEvent : Event
{
    private PlayerData playerData = new();
    public override IEnumerator execute(EventObject eventObject = null)
    {
        playerData.isFirst = false;
        playerData.sceneName = SceneManager.GetActiveScene().name;
        playerData.playerPosition = PlayerCharacter.Instance.GetPlayerPosition();
        playerData.playerDirection = PlayerCharacter.Instance.GetPlayerDirection();

        DataManager.Instance.SaveSection(playerData, "PlayerData");

        yield return null;
    }
}
```
예시로 플레이어의 데이터를 저장하는 이벤트 코드입니다. PlayerData를 만들어서 현재의 정보를 넣어주고 그걸 SaveSection하는 방식으로 이용합니다.    
아래는 실제 사용예시 입니다.

https://github.com/user-attachments/assets/179d3c09-7601-4c6f-98db-2bc29c1b6bf9    


----


- #### 문제점 및 개선
처음 코드를 짤 때 오류가 발생하지 않아서 오래동안 인지하지 못한 오류가 있었습니다.    
작업 후 에디터에서만 확인했기 때문에 발생한 문제인데, 빌드 후 프로그램으로 돌려보니 오류가 발생했습니다.
```csharp
private readonly string savePath = Application.dataPath + "/Database";
```
바로 이 부분 savePath를 정의해주는 부분이 문제였습니다.    
에디터에서는 Application.dataPath를 이용하면 잘 작동했지만, 빌드 시에는 실제 컴퓨터 파일 경로 내부에 지정을 해줘야해서 오류가 발생합니다.    
둘의 차이점은 저장경로의 위치입니다.    
**DataPath**는 읽기 전용이며, 런타임 중에 파일 수정, 작성을 할 수 없습니다. 또한 저장경로가 프로젝트 폴더 내부입니다(Asset).    
**PersistantDataPath**는 특정 운영체제에서 앱이 사용할 수 있도록 허용한 경로입니다.    
즉 저장경로가 다음과 같이 C:\Users\[user name]\AppData\LocalLow\[company name]\[product name] 실제 컴퓨터에서 사용하는 경로로 지정됩니다.    
이러한 차이 때문에 에디터에서가 아닐 때 주의가 필요합니다.    

```csharp
#if UNITY_EDITOR
    private readonly string savePath = Application.dataPath + "/Database";
#else
    private string savePath;
#endif

private void Awake()
{
#if UNITY_EDITOR
#else
    savePath = Path.Combine(Application.persistentDataPath, "Database");
#endif
}
```
그래서 위와 같이 에디터일 때와 아닐때를 구분해서 에디터가 아닐 때, dataPath가 아닌 persistentDataPath를 이용해서 정의해 줬습니다.    
또 변수를 초기화하는 부분도 함수내부(여기선 이벤트 함수 Awake)에서 해줘야합니다.    
이후 빌드 시에도 잘 작동하게 되었습니다.
