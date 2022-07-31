
#### 变量
- WorldList
```c++

TIndirectArray<FWorldContext>	WorldList;

/** The view port representing the current game instance. Can be 0 so don't use without checking. */
UPROPERTY()
class UGameViewportClient* GameViewport;

```
#### 成员函数
- tick
- init
	- create GWorld
```c++
/** Update everything. */
virtual void Tick( float DeltaSeconds, bool bIdleMode ) PURE_VIRTUAL(UEngine::Tick,);
//
// Initialize the engine.
//
void UEngine::Init(IEngineLoop* InEngineLoop){
	if (GIsEditor)
	{
		// Create a WorldContext for the editor to use and create an initially empty world.
		FWorldContext &InitialWorldContext = CreateNewWorldContext(EWorldType::Editor);
		InitialWorldContext.SetCurrentWorld( UWorld::CreateWorld( EWorldType::Editor, true ) );
		GWorld = InitialWorldContext.World();
	}
}
```
- start()
- 