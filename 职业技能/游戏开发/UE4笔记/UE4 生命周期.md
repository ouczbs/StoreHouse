# World

## InitializeActorsForPlay

AGameMode ->InitGame

## BeginPlay

## Tick

# Level

## Serialize

# Actor

## Initialize

```mermaid
sequenceDiagram
title: Initialize
participant Functions as 函数调用
	autonumber
	Functions->>+UWorld: SpawnActor
	UWorld ->>Functions:NewObject<AActor>
	UWorld ->>FLuaContext:...NotifyUObjectCreated
	FLuaContext ->>UUnLuaManager:Bind
	UUnLuaManager ->>Lua:Initialize
	UWorld->>-Functions:SpawnActor
```



## UserConstructionScript

```mermaid
sequenceDiagram
title: UserConstructionScript
participant Functions as 函数调用
	autonumber
	Functions->>+UWorld: SpawnActor
	UWorld ->>AActor:PostSpawnInitialize
	AActor ->>AActor:ExecuteConstruction
	AActor ->>AActor:ProcessUserConstructionScript
	AActor ->>AActor:UserConstructionScript
	UWorld->>-Functions:SpawnActor
```



## ReceiveBeginPlay

```mermaid
sequenceDiagram
title: ReceiveBeginPlay
participant Functions as 函数调用
	autonumber
	Functions->>+UWorld: BeginPlay
	UWorld ->>AGameModeBase:StartPlay
	AGameModeBase ->>AGameStateBase:HandleBeginPlay
	AGameStateBase ->>AWorldSettings:NotifyBeginPlay
	AWorldSettings ->>AActor:DispatchBeginPlay
	AActor ->>AActor:BeginPlay
	AActor ->>AActor:ReceiveBeginPlay
	UWorld->>-Functions:BeginPlay
```



## ReceiveTick

```mermaid
sequenceDiagram
title: ReceiveTick
participant Functions as 函数调用
	autonumber
	Functions->>+UWorld: Tick
	UWorld ->>FTickTaskManagerInterface:StartPlay
	FTickTaskManagerInterface ->>AActor:...
	AActor ->>AActor:ReceiveTick
	UWorld->>-Functions:Tick
```



## ReceiveEndPlay

```mermaid
sequenceDiagram
title: ReceiveEndPlay
participant Functions as 函数调用
	autonumber
	Functions->>+UWorld: DestroyActor
	UWorld ->>AActor:Destroyed
	AActor ->>AActor:RouteEndPlay
	AActor ->>AActor:EndPlay
	AActor ->>AActor:ReceiveEndPlay
	UWorld ->>AActor:ReceiveDestroyed
	UWorld->>-Functions:DestroyActor
```

# Object

## Destory

