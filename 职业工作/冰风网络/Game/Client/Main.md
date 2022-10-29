# 初始化流程

## Main

- 匹配不同的设备

## wWinMain

- 处理命令行消息

## GameMain

通过XApp单例控制游戏流程

- GameMainInit
- GameMainRun
- GameMainExit

## GameApp

- startup
  - 根据参数配置游戏 
    - 是否出错，资源不全
    - 默认渲染窗口配置等
  - OnInit
    - 配置具体的字体纹理等
    - startGame
      - 设置计时器
      - 创建Lua栈
        - G_GetLocalConfig
        - G_SaveLocalConfig
        - 初始化 app.lua
      - 设置语言dv
      - 设置声音
      - 设置事件管理
      - 网络消息
      - ....
  - 初始化CEGUI
    - 初始化鼠标图标

# GM指令

GUIGM

GMCMD

# UI界面

## 配置

- Config.xml
  - id
  - name
  - layout
  - script
  - class
  - UILayer
  - sound
  - effecct
  - safearea

## CEGUI

### 窗口

CEGUI::Window

- : public PropertySet
  - : public PropertyReceiver

- 18121060341

## UI框架

C++

- GuiManager
  - 创建、销毁、显示隐藏，UI层级
  - UI交互事件管理
  - UI音乐、特效、动画
  - createGui(U32 id , CEGUI::Window* parent)
  - createGui(string& name, CEGUI::Window* parent)


Lua

- ActivityMain.lua
  - onFunBtnClick
- common.lua
  - G_CreateGuiByID
- 

## UI类型

- 登录

  - GuiHealth

  - LoginGui

  - SelectRole

  - LoadFrame

- 游戏
  - JoyStick
  - FightPetInletWin
  - BubbleLeft
  - ...

# 事件管理

## 类和对象

- EventManager
  - subscribe
- GameEvent::EnterFrame

# 网络通信

## 数据结构

- Lua

```lua
reqXxxCmd = 
{
    {"xxx1",U32|U16|U8 , false|true, 0},
    ...,
    type = id
    name = "reqXxxCmd"
}

function reqXxxCmd:new()
    local cmd = {structDef = self}
    return cmd
end
```

- C++

```c++
//导出工具依据lua代码自动生成C++代码
struct reqXxxCmd : public stNullUserCmd
{
    ....;
    void pack(){
        ...
    }
    void unpack(){
        ...
    }
}
```



## 数据传输

- C++

```C++
int luaSendMsg(lua_State * L){
	Vector<unit8 , allocator<unit8>> buf;
    pushLuaTable(L,buf);//从lua读取 stNullUserCmd 数据
    stNullUserCmd* pCmd = (stNullUserCmd*) &buf[4];//前四个字节为地址
    NetSendCommand(pcmd, buf.size - 4);//数据长度
}

void NetSendCommand(stNullUserCmd * pcmd, uint32 buffersize , bool bEnableCompress){
    //发送数据
    //统计数据发送间隔
    //压缩数据
}
```

- Lua

```lua
function cmdfunc(params)--由c++调用
    local cmd = reqXxxCmd:new()
    --cmd... = params...
    luaSendMsg(cmd);
end
```

## 数据订阅

```c++
struct stMsg{
    U8* buffer;
    // --2--| size  |
    // type | data  |
    U32 rawSize;
}
```

# 游戏架构

- NPC
- MainPlayer
  - Player
- World
  - 地形
  - 天气
  - 天空盒

- GameScene
  - World
  - EntityManager
    - Entity
  - EntityMoveManager
  - BufferStateManager
  - SystemSetManager
  - SnowWarAreaManager
  - ....

- GameStateManager
  - GameStateLogin
  - GameStateSelectRole
  - GameStateMain
- GameWindow
  - RenderWindow

- GameApp
  - 。。。

- GameMain
  - GameMainInit
  - GameMainRun
  - GameMainExit


# 游戏设计

 ## 结构

- 游戏输入
  - 网络输入
  - 键盘输入
- 处理输入
  - 行为树
  - 动画机
- 游戏渲染
  -  

## 玩法

- BUFFER
- UI
- BOSS
- AI
- Anim
- Render
- 特效



那么相机呢？

物体 场景 世界
