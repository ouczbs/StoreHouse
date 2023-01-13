# 指针

## 共享指针

- TSharedPtr

## 引用指针

- TSharedRef

## 弱指针

- TWeakPtr

- TWeakObjectPtr

  - 表示不真正引用UObject，而导致这个UObject不能被GC

## 资源指针

### 资源路径

- FSoftObjectPath

  - 配置一个具体资源(非蓝图资源和蓝图资源都可以)的路径
  - 是其他资源指针的转化基础
- FSoftClassPath

### 资源对象

- FSoftObjectPtr

  - 软对象指针

  - ```c++
    FSoftObjectPath meshFilePath;
    FSoftObjectPtr meshObjectPtr = FSoftObjectPtr(meshFilePath);
    ```

  - 

- TSoftObjectPtr 

  - FSoftObjectPtr的模板
  
  - 省掉了“Cast”转为对象的过程
  
  - ```
    TSoftObjectPtr<UStaticMesh> StaticMeshPtr = TSoftObjectPtr<UStaticMesh>(meshFilePath);
    UStaticMesh* pStaticMesh = StaticMeshPtr.Get();
    if (pStaticMesh)
    {
    	UE_LOG(LogTemp, Error, TEXT("UStaicMesh name is %s"), *pStaticMesh->GetName());
    }
    ```
  
    - ```
      UCLASS(Blueprintable)
      class ZWORLD_API UStringMapping : public UObject
      {
      	GENERATED_BODY()
      public:
      
      	UPROPERTY(EditAnywhere, Category = Mapping)
      	TMap<FName, FName>	Mapping;
      
      	// Add/delete mapping
      	void AddMapping(const FName& Key, const FName& Value) {
      		FName& TargetNode = Mapping.Add(Key);
      		TargetNode = Value;
      	};
      	void DeleteMapping(const FName& Key) {
      		Mapping.Remove(Key);
      	};
      };
      ```
  
    - 

### 资源 UClass
TObjectPtr
- TSoftClassPtr

  - TSoftClassPtr来检测蓝图资源加载而成的UClass*

  - ```
    	TSoftClassPtr<AActor> ActorClassPtr = TSoftClassPtr<AActor>(SoftBPClassPathName);
    ```

  - 

- TSubclassOf

  - ```c++
    作用同
    UPROPERTY(EditAnywhere)
    UClass* actorClass;
    ```

# 共享指针

## 语法

```c++

// 为新对象创建一个共享指针
TSharedPtr<FMyObjectType> NewPointer(new FMyObjectType());
// 从共享引用创建一个共享指针
TSharedRef<FMyObjectType> NewReference(new FMyObjectType());
TSharedPtr<FMyObjectType> PointerFromReference = NewReference;
// 创建一个线程安全的共享指针
TSharedPtr<FMyObjectType, ESPMode::ThreadSafe> NewThreadsafePointer = MakeShared<FMyObjectType, ESPMode::ThreadSafe>(MyArgs);
    
//判断指针是否为空
isValid();
```

## 功能

- 持有对象指针和共享引用
- 当引用为0时自动销毁对象
- 指针可为空

# 引用指针

## 语法

```c++
//分配新的数据对象，并创建对其的强引用。
TSharedRef<FMyObjectType> ObjectOwner = MakeShared<FMyObjectType>();
//以下两者均不会编译：
TSharedRef<FMyObjectType> UnassignedReference;
TSharedRef<FMyObjectType> NullAssignedReference = nullptr;
//以下会编译，但如NullObject实际为空则断言。
TSharedRef<FMyObjectType> NullAssignedReference = NullObject;
```

## 功能

- 指针不可为空
- 可转化为共享指针

# 弱指针

## 语法

```c++
//创建指向新数据对象的弱指针。
TWeakPtr<FMyObjectType> ObjectObserver(ObjectOwner);
TWeakPtr<FMyObjectType> AnotherObjectObserver = ObjectObserver;
//Pin转化弱指针到共享指针
TSharedPtr<FMyObjectType> LockedObserver = ObjectObserver.Pin()
```

## 功能

- 指针可为空
- 不影响对象销毁
- 可转化为共享指针

# 函数库
## 类

| 助手              | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| `TSharedFromThis` | 在添加 `AsShared` 或 `SharedThis` 函数的 `TSharedFromThis` 中衍生类。利用此类函数可获取对象的 `TSharedRef`。 |

## 函数

| 助手                                           | 描述                                                         |
| ---------------------------------------------- | :----------------------------------------------------------- |
| `MakeShared` 和 `MakeShareable`                | 在常规C++指针中创建共享指针。`MakeShared` 会在单个内存块中分配新的对象实例和引用控制器，但要求对象提交公共构造函数。`MakeShareable` 的效率较低，但即使对象的构造函数为私有，其仍可运行。利用此操作可拥有非自己创建的对象，并在删除对象时支持自定义行为。 |
| `StaticCastSharedRef` 和 `StaticCastSharedPtr` | 静态投射效用函数，通常用于向下投射到衍生类型。               |
| `ConstCastSharedRef` 和 `ConstCastSharedPtr`   | 将 `const` 智能引用或智能指针分别转换为 `mutable` 智能引用或智能指针 |