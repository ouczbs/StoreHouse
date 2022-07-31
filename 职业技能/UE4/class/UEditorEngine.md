

### 成员变量
```c++
/** A pointer to a UWorld that is the duplicated/saved-loaded to be played in with "Play From Here" 								*/
UPROPERTY()
class UWorld* PlayWorld;

/** When Simulating In Editor, a pointer to the original (non-simulating) editor world */
UPROPERTY()
class UWorld* EditorWorld;

/** List of all viewport clients */
TArray<class FEditorViewportClient*> AllViewportClients;
```
### 成员函数
```c++

/** Creates an GameInstance with the given settings. A window is created if this isn't a server. */
UGameInstance* UEditorEngine::CreateInnerProcessPIEGameInstance(FRequestPlaySessionParams& InParams, const FGameInstancePIEParameters& InPIEParameters, int32 InPIEInstanceIndex)
{
	UGameInstance* GameInstance = NewObject<UGameInstance>(this, GameInstanceClass);
	// We need to temporarily add the GameInstance to the root because the InitializeForPlayInEditor
	// call can do garbage collection wiping out the GameInstance
	GameInstance->AddToRoot();
	const FGameInstancePIEResult InitializeResult = GameInstance->InitializeForPlayInEditor(InPIEInstanceIndex, InPIEParameters);
	// Our game instance was successfully created
	FWorldContext* const PieWorldContext = GameInstance->GetWorldContext();
	check(PieWorldContext);
	PlayWorld = PieWorldContext->World();
}

```

