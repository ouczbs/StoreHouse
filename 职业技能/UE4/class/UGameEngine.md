
### 变量
```c++
UPROPERTY(transient)
UGameInstance* GameInstance;


/** The game viewport window */
TWeakPtr<class SWindow> GameViewportWindow;
/** The primary scene viewport */
TSharedPtr<class FSceneViewport> SceneViewport;
/** The game viewport widget */
TSharedPtr<class SViewport> GameViewportWidget;
```