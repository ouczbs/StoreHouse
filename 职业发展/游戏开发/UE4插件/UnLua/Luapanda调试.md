# LuaPanda

[luapanda](https://github.com/Tencent/LuaPanda)

# 注册luasocket.dll库

- Lua `package.cpath = package.cpath .. ";c:/luasocket/?.dll";`

# 添加LuaPanda

# 启动LuaPanda

require("LuaPanda").start("127.0.0.1",8818)

# unlua 注册

```c++
/**
 * Add a package path to package.path
 */
 UNLUA_API void AddPackageCPath(lua_State* L, const char* cPath)
{
    if (!cPath)
    {
        //UE_LOG(LogUnLua, Warning, TEXT("%s, Invalid package cpath!"), ANSI_TO_TCHAR(FUNCTION));
        return;
    }

    lua_getglobal(L, "package");
    lua_getfield(L, -1, "cpath");
    char FinalPath[MAX_SPRINTF];
    FCStringAnsi::Sprintf(FinalPath, "%s;%s", lua_tostring(L, -1), cPath);
    lua_pushstring(L, FinalPath);
    lua_setfield(L, -3, "cpath");
    lua_pop(L, 2);
}
void AddPackagePath(lua_State *L, const char *Path)
{
    if (!Path)
    {
        UE_LOG(LogUnLua, Warning, TEXT("%s, Invalid package path!"), ANSI_TO_TCHAR(__FUNCTION__));
        return;
    }

    lua_getglobal(L, "package");
    lua_getfield(L, -1, "path");
    char FinalPath[MAX_SPRINTF];
    FCStringAnsi::Sprintf(FinalPath, "%s;%s", lua_tostring(L, -1), Path);
    lua_pushstring(L, FinalPath);
    lua_setfield(L, -3, "path");
    #if WITH_EDITOR
        #if PLATFORM_WINDOWS
        // add new package cpath
        FString LuaSrcCPath = GLuaSrcFullPath + TEXT("core.dll");
        AddPackageCPath(L, TCHAR_TO_UTF8(*LuaSrcCPath));// 这个函数是后来自己加的，对照AddPackagePath重写的,为了能require luasocket

        // Create 'DEBUG' namespace (a Lua table)
        lua_pushboolean(L, true);
        lua_setglobal(L, "WITH_LUAIDE_DEBUG");//注册WITH_LUAIDE_DEBUG宏，在lua里用
        #endif
    #endif
    lua_pop(L, 2);
}
```



# unlua

目前unlua默认不集成luasocket，需要安装调试依赖的luasocket库，之后再进行调试接入。

1. 安装luasocket
   - luasocket源码推荐使用 https://github.com/diegonehab/luasocket 。我们用此源码编译了luasocket库文件并放在项目的/luasocketBin/下。
   - mac下可以选择 **源码编译/插件管理工具安装/拷贝库文件** 的方式，把`socket和mime文件夹`部署到/usr/local/lib/lua/5.3/ 目录下，运行时可以自动被引用到， 部署完成后调用`require("socket.core");` 验证是否有报错。
   - win下可以选择 **源码编译/拷贝库文件** 的方式，把luascoket拷贝到自定义位置，并在lua代码中修改package.cpath，使库文件可以被引用到。比如部署在c:/luasocket下，cpath要修改为`package.cpath = package.cpath .. ";c:/luasocket/?.dll";`, 并用 `require("socket.core");` 验证是否有报错。
2. **放入调试器文件** 把github中 /Debugger 下的 `LuaPanda.lua` 文件拷贝到unlua工程 `unlua/Content/Script/`下，和UnLua.lua 文件同级
3. **配置工程** 把`Script`文件夹放入 VSCode , 点击 VSCode 调试选项卡下的齿轮图标，选择 LuaPanda。打开生成的 `.vscode/launch.json` 文件,  **调整其中的stopOnEntry为 false。**
4. **开始调试** 在执行的lua代码中加入`require("LuaPanda").start("127.0.0.1",8818)` 。VSCode切换到调试选项卡，配置项选择`LuaPanda`， 点击 VSCode 的开始调试箭头，再运行ue4，在加入 require 的位置后会自动停止。之后可以打断点调试。