
#### 变量
```c++
/** Array of all actors in this level, used by FActorIteratorBase and derived classes */
TArray<AActor*> Actors;

/** Array of actors to be exposed to GC in this level. All other actors will be referenced through ULevelActorContainer */
TArray<AActor*> ActorsForGC;

/** 
 * The World that has this level in its Levels array. 
 * This is not the same as GetOuter(), because GetOuter() for a streaming level is a vestigial world that is not used. 
 * It should not be accessed during BeginDestroy(), just like any other UObject references, since GC may occur in any order.
 */
UPROPERTY(transient)
UWorld* OwningWorld;

/** The level scripting actor, created by instantiating the class from LevelScriptBlueprint.  This handles all level scripting */
UPROPERTY(NonTransactional)
class ALevelScriptActor* LevelScriptActor;

UPROPERTY()
AWorldSettings* WorldSettings; //( Actors[0] )
```