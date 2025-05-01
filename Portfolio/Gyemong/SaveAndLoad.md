### 세이브 및 로드 구현하기    
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
        string encryptedData = Encrypt(json); // JSON 데이터를 암호화
        string filePath = Path.Combine(savePath, fileName + ".json");

        // 파일의 읽기 전용 속성을 해제 (파일이 이미 존재할 경우)
        if (File.Exists(filePath))
        {
            FileAttributes attributes = File.GetAttributes(filePath);
            if (attributes.HasFlag(FileAttributes.ReadOnly))
            {
                File.SetAttributes(filePath, attributes & ~FileAttributes.ReadOnly);
            }
        }

        // 파일 저장
        File.WriteAllText(filePath, encryptedData);

        // 파일을 다시 읽기 전용으로 설정
        File.SetAttributes(filePath, FileAttributes.ReadOnly);

        Debug.Log($"{fileName} saved at {savePath} as ReadOnly and encrypted.");
    }

    // AES 암호화
    private string Encrypt(string plainText)
    {
        using Aes aes = Aes.Create();
        aes.Key = Encoding.UTF8.GetBytes(encryptionKey);
        aes.IV = new byte[16]; // 16-byte 초기화 벡터 (0으로 초기화)
        using var encryptor = aes.CreateEncryptor(aes.Key, aes.IV);
        using var ms = new MemoryStream();
        using (var cs = new CryptoStream(ms, encryptor, CryptoStreamMode.Write))
        using (var writer = new StreamWriter(cs))
        {
            writer.Write(plainText);
        }
        return Convert.ToBase64String(ms.ToArray()); // 암호화된 데이터를 Base64로 인코딩
    }
```
데이터를 저장하는 코드입니다. 여기서 데이터를 암호화 한 후, 읽기 전용으로 설정합니다.
