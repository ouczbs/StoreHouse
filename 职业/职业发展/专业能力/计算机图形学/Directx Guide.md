# Guide

https://zhuanlan.zhihu.com/p/519905706

## 符合模式


## Header

```c++
#include <d3d12.h> // header
#include <d3dcompiler.h> //struct class ...
#include <dxgi.h> //factory

#pragma comment(lib,"d3d12.lib")
#pragma comment(lib,"d3dcompiler.lib")
#pragma comment(lib, "dxgi.lib")

//#define IID_PPV_ARGS

```



## MakeDevice

```c++


CreateDXGIFactory(IID_PPV_ARGS(&pdxgiFactory));
```

## MakeSwapChain

