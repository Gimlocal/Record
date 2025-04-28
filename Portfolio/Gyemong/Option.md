### 옵션 창 구현하기.    
#### 주요 구현 내용    
- 옵션창에 필요한 버튼 및 관련 기능
- 사운드 조절 기능
- 키 바인딩 기능

#### 1. 옵션창에 필요한 버튼 및 관련 기능 
![image](https://github.com/user-attachments/assets/a5b0258e-ff23-4e12-bd01-6c137872231a)    
순서대로 게임 재개, 사운드 조절, 키 바인딩, 게임 종료 버튼으로    
게임 재개는 옵션창을 끄는 기능, 게임 종료는 타이틀로 나가는 기능을 담당합니다.    
```cpp
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
```cpp
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
