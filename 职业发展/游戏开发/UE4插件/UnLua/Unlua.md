# 修改模板
```
FString ModuleName = FString::Printf(TEXT("--GameWorld/%s%s.lua"), *OuterPath, *ClassName);
ModuleName = ModuleName.Replace(TEXT("/"), TEXT("."));
ModuleName = ModuleName.LeftChop(4) + TEXT("\n");//去掉 .lua
Content = ModuleName + Content;
```

# 修改报错

## 原理

```c++
    int luaL_loadbuffer (lua_State *L,
                         const char *buff,
                         size_t sz,
                         const char *name);
	
```

- name 传路径太长了
- 改为传文件名

## 修改 LuaEnv

```c++
class FLuaEnv{
    bool LoadString(const TArray<uint8>& Chunk, const FString& ChunkName = "chunk" ,const FString& FileName = "UnLua.lua");
} 
	bool LoadString(const TArray<uint8>& Chunk, const FString& ChunkName = "chunk" ,const FString& FileName = "UnLua.lua");

        bool LoadString(const FString& Chunk, const FString& ChunkName = "chunk", const FString& FileName = "UnLua.lua");

        bool LoadBuffer(const char* Buffer, const size_t Size, const char* InName, const char* fileName = "ouczbs@qq.com");

```



