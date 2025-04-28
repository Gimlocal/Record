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
OpenOrCloseOption() 함수는 범용적으로 상속받아서 쓰이는 UI를 끄는 함수입니다.    
추가적으로로 게임 종료 버튼은 타이틀에서 안보이고, OptionUIToggler.Instance.ToggleOption()을 통해 OpenOrCloseOption()을 불러옵니다.


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

