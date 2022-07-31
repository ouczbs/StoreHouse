
#### 变量
```c++
FEngineLoop	GEngineLoop;
```
#### 函数
- GuardedMain
- EditorInit
- EngineInit
```c++
/**
 * Static guarded main function. Rolled into own function so we can have error handling for debug/ release builds depending
 * on whether a debugger is attached or not.
 */
int32 GuardedMain( const TCHAR* CmdLine ){
#if WITH_EDITOR
		if (GIsEditor)
		{
			ErrorLevel = EditorInit(GEngineLoop);
		}
		else
#endif
		{
			ErrorLevel = EngineInit();
		}
}


int32 EditorInit( IEngineLoop& EngineLoop )
{
	int32 ErrorLevel = EngineLoop.Init();
}

/** 
 * Inits the engine loop 
 */
int32 EngineInit()
{
	int32 ErrorLevel = GEngineLoop.Init();

	return( ErrorLevel );
}

```