

```c++
#include "Subsystems/GameInstanceSubsystem.h"
UGameInstanceSubsystem

#include "Subsystems/GameInstanceSubsystem.h"
UGameInstanceSubsystem

 virtual void Initialize(FSubsystemCollectionBase& Collection)override;
 virtual void Deinitialize()override;

UFUNCTION(BlueprintCallable, Category = "GameLibrary")
 static UGameInstanceSubsystem* GetGameSubsystem(TSubclassOf<UGameInstanceSubsystem> Class)
 {
     if (const UGameInstance* GameInstance = GWorld->GetGameInstance())
     {
     	return GameInstance->GetSubsystemBase(Class);
     }
     return nullptr;

 }
```

# 生命周期

