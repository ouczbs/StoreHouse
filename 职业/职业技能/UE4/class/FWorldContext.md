
#### 变量
- GameViewport
- OwningGameInstance
- ActiveNetDrivers
- ExternalReferences
```c++

UWorld*	ThisCurrentWorld;
	
	
UPROPERTY()
class UGameViewportClient* GameViewport;

UPROPERTY()
class UGameInstance* OwningGameInstance;

/** A list of active net drivers */
UPROPERTY(transient)
TArray<FNamedNetDriver> ActiveNetDrivers;


/** Outside pointers to CurrentWorld that should be kept in sync if current world changes  */
TArray<UWorld**> ExternalReferences;