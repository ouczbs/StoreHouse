# WindowAPI

## 数据类型

![image-20220831163307272](F:\ouczbs\StoreHouse\职业发展\专业能力\操作系统\assets\image-20220831163307272.png)

## 常用宏

### 高低位

- 16
  - HIBYTE
  - LIBYTE
- 32
  - HIWORD
  - LOWORD

### 合并

- MAKEWORD
- MAKELONG

## 句柄

标识Windows资源（窗口、菜单、位图）和设备等的数据指针类型

通过标识间接访问windows系统内部的资源



# 窗口程序

## 程序结构

- 系统数据
- 窗口数据
- 窗口与系统交互
- 窗口与窗口交互
- 窗口内部的交互

## 消息机制

### 消息流向

- 系统消息
- 应用消息
- 窗口消息
- 控件消息

### 消息队列

- 系统消息队列
- 应用消息队列

### 消息处理

- 入队消息
  - 取出消息
  - 转换消息
  - 分发消息
- 非入队消息
  - 直接处理

## 程序代码

vs ---> c++ --> 创建窗口应用程序

```c++
#include <windows.h>
//入口函数
int WinMain(...){
    //init window instance
    CreateWindowW();
    ShowWindow();
    UpdateWindow();
    //message loop
    while(GetMessage()){
        //快捷键， 可省略
        TranslateAccelerator();
        TranslateMessage();
        DispatchMessage();
    }
}
//子线程
//  WM_COMMAND  - 处理应用程序菜单
//  WM_PAINT    - 绘制主窗口
//  WM_DESTROY  - 发送退出消息并返回
//  WM_KeyONDOWN - 按键消息
LRESULT CALLBACK    WndProc(HWND, UINT, WPARAM, LPARAM);

```

# 程序设计

## 匈牙利命名法

![image-20220831165533819](F:\ouczbs\StoreHouse\职业发展\专业能力\操作系统\assets\image-20220831165533819.png)

## Direct3D

```c
#pragma comment(lib,"dxerr.lib")
#pragma comment(lib,"dxguid.lib")
#pragma comment(lib,"d3d9.lib")
```

