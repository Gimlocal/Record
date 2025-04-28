### 옵션 창 구현하기.    
#### 주요 구현 내용    
- 옵션창에 필요한 버튼 및 관련 기능
- 사운드 조절 기능
- 키 바인딩 기능

#### 1. 옵션창에 필요한 버튼 및 관련 기능 
![image](https://github.com/user-attachments/assets/a5b0258e-ff23-4e12-bd01-6c137872231a)    
순서대로 게임 재개, 사운드 조절, 키 바인딩, 게임 종료 버튼으로    
게임 재개는 옵션창을 끄는 기능, 게임 종료는 타이틀로 나가는 기능을 담당합니다.    
```csharp
protected void OpenOrCloseOption()
{
    _isOptionOpened = !_isOptionOpened;
    _window.SetActive(_isOptionOpened);
    if (SceneManager.GetActiveScene().name != "TitleScene")
    {
        PlayerCharacter.Instance.SetPlayerMove(!_isOptionOpened);
    }
}
```
```csharp
public class OptionExitButton : MonoBehaviour
{
    private void Start()
    {
        if (SceneManager.GetActiveScene().name == "TitleScene")
        {
            gameObject.SetActive(false);
        }
    }

    public void ExitButton()
    {
        gameObject.SetActive(false);
        OptionUIToggler.Instance.ToggleOption();
        Destroy(PlayerCharacter.Instance.gameObject);
        SceneManager.LoadScene("TitleScene");
    }
}
```
OpenOrCloseOption 함수는 범용적으로 상속받아서 쓰이는 UI를 키고 끄는 함수입니다.    
추가적으로 게임 종료 버튼은 타이틀에서 안보이고, OptionUIToggler.Instance.ToggleOption을 통해 OpenOrCloseOption을 불러옵니다.


#### 2. 사운드 조절 기능    
옵션창에 사운드 버튼을 눌렀을 때 나오는 창 입니다.    
![image](https://github.com/user-attachments/assets/1ac9106d-8b54-4a08-83cd-5ad387e5b063)    
Master는 모든 소리를 조절, UI, Bgm, Effect는 각 종류의 소리를 조절합니다.
```csharp
public class SoundController : SingletonObject<SoundController>
{
    public Scrollbar masterVolumeSlider;
    public Scrollbar uiVolumeSlider;
    public Scrollbar bgmVolumeSlider;
    public Scrollbar effectVolumeSlider;

    private void Start()
    {
        InitialSetting();
    }

    private void InitialSetting()
    {
        masterVolumeSlider = transform.Find("MasterVolumeSlider").GetComponent<Scrollbar>();
        uiVolumeSlider = transform.Find("UIVolumeSlider").GetComponent<Scrollbar>();
        bgmVolumeSlider = transform.Find("BgmVolumeSlider").GetComponent<Scrollbar>();
        effectVolumeSlider = transform.Find("EffectVolumeSlider").GetComponent<Scrollbar>();

        SoundManager.Instance.SetMasterVolume(soundData.masterVolume);
        SoundManager.Instance.SetVolume(SoundType.UI, soundData.UIVolume);
        SoundManager.Instance.SetVolume(SoundType.BGM, soundData.bgmVolume);
        SoundManager.Instance.SetVolume(SoundType.EFFECT, soundData.sfxVolume);

        masterVolumeSlider.onValueChanged.AddListener(UpdateMasterVolume);
        uiVolumeSlider.onValueChanged.AddListener(UpdateUIVolume);
        bgmVolumeSlider.onValueChanged.AddListener(UpdateBgmVolume);
        effectVolumeSlider.onValueChanged.AddListener(UpdateEffectVolume);
    }

    private void UpdateMasterVolume(float value)
    {
        SoundManager.Instance.SetMasterVolume(value);
    }

    private void UpdateUIVolume(float value)
    {
        SoundManager.Instance.SetVolume(SoundType.UI, value);
    }

    private void UpdateBgmVolume(float value)
    {
        SoundManager.Instance.SetVolume(SoundType.BGM, value);
    }

    private void UpdateEffectVolume(float value)
    {
        SoundManager.Instance.SetVolume(SoundType.EFFECT, value);
    }
    
}
```
UI에서 자식으로 들어있는 각각의 사운드 바(게임 오브젝트)를 불러와서 Listener를 이용해 값 변동을 감지해 소리를 조절해줍니다.    
UI의 Scrolbar를 이용해서 구현했습니다.
```csharp
public void SetVolume(SoundType type, float volume)
{
    volumes[type] = volume;

    foreach (SoundObject soundObject in soundObjects)
    {
        if (soundObject.GetSoundType() == type)
        {
            soundObject.SetVolume(volume);
        }
    }
}
```
SetVolume은 각각의 SoundSource의 value(소리)를 조절해주는 식으로 작동합니다. (master도 마찬가지)    
Back 버튼을 누르면 OpenOrCloseOption함수를 이용해 사운드 조절 창을 끄고 옵션창을 다시 키는 식으로 옵션창으로 돌아갑니다.    


#### 3. 키 바인딩 기능    
옵션창의 키 바인딩 버튼을 눌렀을 때 나오는 창 입니다.
![image](https://github.com/user-attachments/assets/1834432d-71fd-4adb-975e-9216de94f3ae)    
게임 진행시 사용되는 이동, 공격, 돌진, 옵션, 메뉴 등의 키를 바인딩 할 수 있는 창입니다.    
```csharp
public class KeyMapping : MonoBehaviour
{
    [SerializeField] private ActionCode actionCode;

    private void BindKey(KeyCode newKey)
    {
        CheckDuplication(newKey);
        InputManager.Instance.SetKey(actionCode, newKey);
    }
    
    public void OnClickButton()
    {
        StartCoroutine(WaitForKeyInput());
    }
    
    private IEnumerator WaitForKeyInput()
    {
        yield return new WaitUntil(() => Input.anyKeyDown);
        
        KeyCode pressedKey = GetPressedKey();
        BindKey(pressedKey);
        KeyButtonTexts.Instance.UpdateKeyText();
    }

    private KeyCode GetPressedKey()
    {
        foreach (KeyCode key in System.Enum.GetValues(typeof(KeyCode)))
        {
            if (Input.GetKeyDown(key)) return key;
        }
        return KeyCode.None;
    }
    
    private void CheckDuplication(KeyCode keyCode)
    {
        Dictionary<ActionCode, KeyCode> keyMappings = InputManager.Instance.GetKeyActions();
        List<ActionCode> keysToCheck = new(keyMappings.Keys);

        foreach (ActionCode actionCode in keysToCheck)
        {
            if (keyMappings[actionCode] == keyCode)
            {
                InputManager.Instance.SetKey(actionCode, KeyCode.None);
            }
        }
    }
}
```
각 텍스트의 옆에 버튼을 누를 시 입력을 받아서 입력받은 키를 바인딩 해주는 코드입니다(OnClickButton 함수로 시작). 모든 입력을 받기 때문에 마우스 클릭도 받을 수 있습니다.
중복방지를 위한 기능과 실시간으로 입력받은 키를 적어주는 기능또한 구현했습니다.    
InputManager에서 초기에 Dictionary를 이용해 KeyCode와 ActionCode(enum)를 짝 지어놓고 KeyMapping 코드에서 이를 변경해주는 구조입니다.
Back 버튼은 사운드 조절창과 마찬가지로 옵션창으로 돌아가는 기능을 합니다.
