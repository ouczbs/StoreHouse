# UI设计

## CEGUI 工具

编辑器工具

位于 `/tool/Bin64/CEGUIEditor.exe`

### 使用规范

变量命名尽量全局唯一

### 存储路径

`/trunk/Bin/Data/interfaces/layouts`

## 配置文件

### config.xml

配置窗口数据，绑定窗口与lua类

方便通过ID找到对应窗口和lua类，不易出错

### funBtn.xml

配置主窗口按钮数据，在代码里自动创建在对应窗口中，可用于管理按钮红点

## lua Class

获取窗口config.xml中定义的窗口 `G_GetFrame`

获取存储路径里的任意窗口 `G_LoadWindowLayout`

### lua 窗口类

`init、destory、`

### 代码优化

- 数据分离

- 数据操作分离

- 界面分离

- 事件分离

## 常见问题

- UI点击不生效
  - 子控件超出范围时，父控件 ChildOutOfArea 需要设置为是
  - 点击穿透属性 设置为否

# 网络协议

## 客户端

`luaRegMsg`  注册typeid 与对应的 对象 ， 用于反序列化数据为对象

`G_NetEvent` 绑定typeid 与响应函数 ，用于处理服务器响应的数据对象

### LuaNetMsg.h

`LuaNetMsg::s_mapMsgDescs[type] = strDesc; //LuaCExport(...)`

`LuaNetMsg::s_mapMsgStructDefs[type] = structdef //luaRegMsg(...)`

### NetMsgSubscribe.h

`NetMsgSubscribe::mHandleMapsHash[type] = handles //G_NetEvent(...)`

## 服务端

。。。

## 数据对象

- 数据包 size + data
- 协议数据对象 typeid + data
- 数据编码解码

# 代码设计

层级

- 命名空间
  - 类
    - 成员函数
  - 函数
  - 变量









