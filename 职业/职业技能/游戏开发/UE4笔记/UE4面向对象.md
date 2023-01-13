# UObject

## Actor

```c++
UWorld* World = GetWorld();  
FVector pos(150, 0, 20);  
AMyActor* MyActor = World->SpawnActor<AMyActor>(pos, FRotator::ZeroRotator);   

MyComponent = CreateDefaultSubobject<UMyActorComponent>(TEXT("MyComponent"));  
```



## ActorComponent

```c++
/* <CreateObjectDemo> 
* 创建Component对象，要使用CreateDefaultSubobject模板函数 
*/  
MyComponent = CreateDefaultSubobject<UMyActorComponent>(TEXT("MyComponent"));  
```



## 资源对象

```c++
UStaticMesh* SM_Vase = Cast<UStaticMesh>(StaticLoadObject(UStaticMesh::StaticClass(),  
    NULL,  
    TEXT("/Game/Assets/StaticMeshes/SM_Vase"))  
    );  
 
StaticMeshComponent = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("StaticMeshComponent"));  
StaticMeshComponent->SetStaticMesh(SM_Vase);
```

## Object

```c++
MyObject = NewObject<UMyObject>(); 
Object = UGameplayStatics::SpawnObject(ObjectClass , this);

TArray<UObject*> Objs;
GetObjectsOfClass(UClass::StaticClass(), Objs);
UClass* ActorRef = FindObject<UClass>(ANY_PACKAGE,*FString("AActor"));

```

# UClass

```c++

int32 Global_RegisterClass(lua_State *L)
{
    int32 NumParams = lua_gettop(L);
    if (NumParams < 1)
    {
        UNLUA_LOGERROR(L, LogUnLua, Warning, TEXT("%s: Invalid parameters!"), ANSI_TO_TCHAR(__FUNCTION__));
        return 0;
    }

    RegisterClass(L, lua_tostring(L, 1));
    return 0;
}
int32 UClass_Load(lua_State* L)
{
    int32 NumParams = lua_gettop(L);
    if (NumParams != 1)
    {
        UE_LOG(LogUnLua, Log, TEXT("%s: Invalid parameters!"), ANSI_TO_TCHAR(__FUNCTION__));
        return 0;
    }

    const char* ClassName = lua_tostring(L, 1);
    if (!ClassName)
    {
        UE_LOG(LogUnLua, Log, TEXT("%s: Invalid class name!"), ANSI_TO_TCHAR(__FUNCTION__));
        return 0;
    }

    const TCHAR* Suffix = TEXT("_C");
    FString ClassPath(ClassName);
    int32 Index = INDEX_NONE;
    ClassPath.FindChar(TCHAR('.'), Index);
    if (Index == INDEX_NONE)
    {
        ClassPath.FindLastChar(TCHAR('/'), Index);
        if (Index != INDEX_NONE)
        {
            const FString Name = ClassPath.Mid(Index + 1);
            ClassPath += TCHAR('.');
            ClassPath += Name;
            ClassPath.AppendChars(Suffix, 2);
        }
    }
    else
    {
        if (ClassPath.Right(2) != TEXT("_C"))
        {
            ClassPath.AppendChars(TEXT("_C"), 2);
        }
    }

    FClassDesc* ClassDesc = RegisterClass(L, TCHAR_TO_UTF8(*ClassPath));
    if (ClassDesc && ClassDesc->AsClass())
    {
        UnLua::PushUObject(L, ClassDesc->AsClass());
    }
    else
    {
        lua_pushnil(L);
    }

    return 1;
}
```

