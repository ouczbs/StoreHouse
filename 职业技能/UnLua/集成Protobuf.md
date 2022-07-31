# Protobuf 模块

[github](https://github.com/hxhb/unlua-pb)

# UnLua 添加依赖

## UnLua.Build.cs

```c#
 PrivateIncludePaths.AddRange(

     new string[] {

         "UnLua/Private",

         "ThirdParty/LuaProtobuf/Public",

     }
 );

   

 PrivateDependencyModuleNames.AddRange(

     new string[]

     {

         "Core",

         "CoreUObject",

         "Engine",

         "Slate",

         "InputCore",

         "Projects",

         "Lua",

         "LuaProtobuf",

     }
     
 );
```

# 注册 函数库

## LuaContext.cpp

```c++
#include "LuaProtobuf.h"
void FLuaContext::CreateState(){
    FLuaProtobuf& LuaProtobuf = FModuleManager::LoadModuleChecked<FLuaProtobuf>(TEXT("LuaProtobuf"));
    LuaProtobuf.RegisterLuaLib(L);
}
```

