
## 变量
```c++
/** Global UWorld pointer. Use of this pointer should be avoided whenever possible. */
extern ENGINE_API class UWorldProxy GWorld;


/** The world's navigation data manager */
UPROPERTY(Transient)
class UNavigationSystemBase*				NavigationSystem;

/** The current GameMode, valid only on the server */
UPROPERTY(Transient)
class AGameModeBase*						AuthorityGameMode;

/** The replicated actor which contains game state information that can be accessible to clients. Direct access is not allowed, use GetGameState<>() */
UPROPERTY(Transient)
class AGameStateBase*						GameState;

/** The AI System handles generating pathing information and AI behavior */
UPROPERTY(Transient)
class UAISystemBase*						AISystem;

/** RVO avoidance manager used by game */
UPROPERTY(Transient)
class UAvoidanceManager*					AvoidanceManager;

/** Array of levels currently in this world. Not serialized to disk to avoid hard references.								*/
UPROPERTY(Transient)
TArray<class ULevel*>						Levels;

/** Persistent level containing the world info, default brush and actors spawned during gameplay among other things			*/
UPROPERTY(Transient)
class ULevel*								PersistentLevel;

/** The NAME_GameNetDriver game connection(s) for client/server communication */
UPROPERTY(Transient)
class UNetDriver*							NetDriver;


/** Instance of this world's game-specific networking management */
UPROPERTY(Transient)
class AGameNetworkManager*					NetworkManager;

/** Instance of this world's game-specific physics collision handler */
UPROPERTY(Transient)
class UPhysicsCollisionHandler*				PhysicsCollisionHandler;

/** Array of any additional objects that need to be referenced by this world, to make sure they aren't GC'd */
UPROPERTY(Transient)
TArray<UObject*>							ExtraReferencedObjects;

/**
 * External modules can have additional data associated with this UWorld.
 * This is a list of per module world data objects. These aren't
 * loaded/saved by default.
 */
UPROPERTY(Transient)
TArray<UObject*>							PerModuleDataObjects;

// Level sequence actors to tick first
UPROPERTY(transient)
TArray<AActor*>								LevelSequenceActors;

private:
/** Level collection. ULevels are referenced by FName (Package name) to avoid serialized references. Also contains offsets in world units */
UPROPERTY(Transient)
TArray<ULevelStreaming*> StreamingLevels;

#if WITH_EDITORONLY_DATA
	/** Pointer to the current level being edited. Level has to be in the Levels array and == PersistentLevel in the game.		*/
	UPROPERTY(Transient)
	class ULevel*								CurrentLevel;
#endif

	UPROPERTY(Transient)
	class UGameInstance*						OwningGameInstance;