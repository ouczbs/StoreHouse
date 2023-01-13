
### 变量
```c++
FWorldContext * WorldContext；
                                 
/** List of locally participating players in this game instance */
UPROPERTY()
TArray<ULocalPlayer*> LocalPlayers;

/** Class to manage online services */
UPROPERTY()
class UOnlineSession* OnlineSession;

/** List of objects that are being kept alive by this game instance. Stored as array for fast iteration, should not be modified every frame */
UPROPERTY()
TArray<UObject*> ReferencedObjects;

/** Listeners to PreClientTravel call */
FOnPreClientTravel NotifyPreClientTravelDelegates;

/** Handle for delegate for handling PS4 play together system events */
FDelegateHandle OnPlayTogetherEventReceivedDelegateHandle;
```
### 函数
```c++
FGameInstancePIEResult UGameInstance::InitializeForPlayInEditor(int32 PIEInstanceIndex, const FGameInstancePIEParameters& Params)
{

	// Look for an existing pie world context, may have been created before
	WorldContext = EditorEngine->GetWorldContextFromPIEInstance(PIEInstanceIndex);

	if (!WorldContext)
	{
		// If not, create a new one
		WorldContext = &EditorEngine->CreateNewWorldContext(EWorldType::PIE);
		WorldContext->PIEInstance = PIEInstanceIndex;
	}

	WorldContext->PIEWorldFeatureLevel = Params.WorldFeatureLevel;

	WorldContext->RunAsDedicated = Params.bRunAsDedicated;

	WorldContext->OwningGameInstance = this;
	
	const FString WorldPackageName = EditorEngine->EditorWorld->GetOutermost()->GetName();

	// Establish World Context for PIE World
	WorldContext->LastURL.Map = WorldPackageName;
	WorldContext->PIEPrefix = WorldContext->PIEInstance != INDEX_NONE ? UWorld::BuildPIEPackagePrefix(WorldContext->PIEInstance) : FString();

	// We always need to create a new PIE world unless we're using the editor world for SIE
	UWorld* NewWorld = nullptr;
    
	NewWorld->SetPlayInEditorInitialNetMode(GetNetModeFromPlayNetMode(Params.NetMode, Params.bRunAsDedicated));
	NewWorld->SetGameInstance(this);
	WorldContext->SetCurrentWorld(NewWorld);
	WorldContext->AddRef(EditorEngine->PlayWorld);	// Tie this context to this UEngine::PlayWorld*		// @fixme, needed still?
    
	Init();
	return InitResult;
}

```