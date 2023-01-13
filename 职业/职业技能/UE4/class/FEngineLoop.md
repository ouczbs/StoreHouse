
#### 变量
- GEngine
#### 成员函数
- Init
- Tick
```c++
int32 FEngineLoop::Init()
{
	if( !GIsEditor )
	{
		GEngine = NewObject<UEngine>(GetTransientPackage(), EngineClass);
	}
	else
	{
#if WITH_EDITOR
		GEngine = GEditor = GUnrealEd = NewObject<UUnrealEdEngine>(GetTransientPackage(), EngineClass);
#else
		check(0);
#endif
	}
	return 0;
}

void FEngineLoop::Tick()
{
	GEngine->Tick(FApp::GetDeltaTime(), bIdleMode);
}


```
