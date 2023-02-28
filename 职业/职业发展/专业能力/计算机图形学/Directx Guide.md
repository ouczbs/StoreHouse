# Guide

https://zhuanlan.zhihu.com/p/519905706

# DirectX 11

## Error

智能指针

## Header

```c++
// Header & lib
#include <d3d11.h> // d3d 头文件
#pragma comment(lib,"d3d11.lib")//静态库

#include <wrl.h> //ComPtr 组件对象指针 头文件
using namespace Microsoft::WRL;

// 宏
//#define IID_PPV_ARGS 辅助传参
#ifndef ThrowIfFailed // 异常宏
#define ThrowIfFailed(x)                                              \
{                                                                     \
    HRESULT hr__ = (x);                                               \
    if(FAILED(hr__)) { throw D3DException(hr__, L#x, __FILE__, __LINE__); } \
}
#endif
```

## Device 、SwapChain、Context

```c++
// 定义
ComPtr<ID3D11Device> mDevice;
ComPtr<IDXGISwapChain> mSwapChain;
ComPtr<ID3D11DeviceContext> mContext;

// 创建 参数 width、height、hwnd
DXGI_SWAP_CHAIN_DESC swapchainDescription;
ZeroMemory(&swapchainDescription, sizeof(swapchainDescription));
swapchainDescription.BufferCount = SwapChainBufferCount; //交换链数量 选择双缓冲即可
swapchainDescription.BufferDesc.Width = width;// 渲染目标 宽度
swapchainDescription.BufferDesc.Height = height;//渲染目标 高度
swapchainDescription.BufferDesc.Format = DXGI_FORMAT_R8G8B8A8_UNORM; // 渲染目标 格式 png
swapchainDescription.BufferDesc.RefreshRate.Numerator = 60;
swapchainDescription.BufferDesc.RefreshRate.Denominator = 1;
swapchainDescription.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;//渲染用途
swapchainDescription.OutputWindow = hwnd; // 窗口句柄
swapchainDescription.SampleDesc.Count = 1;//采样数量 处理抗锯齿，一个真实像素对应count个子像素
swapchainDescription.SampleDesc.Quality = 0;//采样质量
swapchainDescription.Windowed = TRUE;// 窗口模式 or 全屏模式
ThrowIfFailed(D3D11CreateDeviceAndSwapChain(NULL, D3D_DRIVER_TYPE_HARDWARE,NULL, NULL, NULL ,0, D3D11_SDK_VERSION,
                                            &swapchainDescription, &mSwapChain, &mDevice, NULL, &mContext));
```

## RenderTargetView

```c++
// 定义
int mCurrBackBuffer = 0;
static const int SwapChainBufferCount = 2;
ComPtr<ID3D11Texture2D> mBackBuffer[SwapChainBufferCount];
ComPtr<ID3D11RenderTargetView> mRenderTargetView[SwapChainBufferCount];

// 创建
for (int i = 0; i < SwapChainBufferCount; i++) {
    ThrowIfFailed(mSwapChain->GetBuffer(0, IID_PPV_ARGS(&mBackBuffer[i])));
    ThrowIfFailed(mDevice->CreateRenderTargetView(mBackBuffer[i].Get(), NULL, &mRenderTargetView[i]));
}

// 渲染
float ClearColor[4] = { 0.0f, 1.0f, 0.0f, 1.0f };
mCurrBackBuffer = (mCurrBackBuffer + 1) % SwapChainBufferCount;
mContext->ClearRenderTargetView(mRenderTargetView[mCurrBackBuffer].Get(), ClearColor);
mContext->OMSetRenderTargets(1, mRenderTargetView[mCurrBackBuffer].GetAddressOf(), NULL);
// Present the information rendered to the back buffer to the front buffer (the screen)
mSwapChain->Present(0, 0);
```

## RenderPipeLine

### HLSL

```C++
//***************************************************************************************
// Transforms and colors geometry.
//***************************************************************************************
//常量缓冲区
cbuffer cbPerObject : register(b0) // 从指定槽 b0 取数据赋值给 cbPerObject
{
	float4x4 gWorldViewProj;
};
//输入数据
struct VertexIn
{
	float3 PosL  : POSITION;
	float4 Color : COLOR;
};
//输出数据
struct VertexOut
{
	float4 PosH  : SV_POSITION;//齐次裁减坐标 语义
	float4 Color : COLOR;
};

VertexOut VS(VertexIn vin)
{
	VertexOut vout;

	// Transform to homogeneous clip space.
	vout.PosH = mul(float4(vin.PosL, 1.0f), gWorldViewProj);

	// Just pass vertex color into the pixel shader.
	vout.Color = vin.Color;

	return vout;
}

float4 PS(VertexOut pin) : SV_Target  // 渲染目标
{
	return pin.Color;
}
```

### ConstantBuffer

```c++
//定义
ComPtr<ID3D11Buffer> mConstantBuffer;

//创建
D3D11_BUFFER_DESC cbd;
cbd.BindFlags = D3D11_BIND_CONSTANT_BUFFER;
cbd.Usage = D3D11_USAGE_DYNAMIC;
cbd.CPUAccessFlags = D3D11_CPU_ACCESS_WRITE;
cbd.MiscFlags = 0u;
cbd.ByteWidth = sizeof(cbTransform);
cbd.StructureByteStride = 0u;
ThrowIfFailed(mDevice->CreateBuffer(&cbd, nullptr, &mConstantBuffer));

//上传数据
cbTransform consts{ DirectX::XMMATRIX(1.0f, 0.0f, 0.0f,-0.5f,
                                      0.0f, 1.0f, 0.0f,0.0f,
                                      0.0f, 0.0f, 1.0f,0.0f,
                                      0.0f, 0.0f, 0.0f,1.0f) };
D3D11_MAPPED_SUBRESOURCE msr;
ThrowIfFailed(mContext->Map(
    mConstantBuffer.Get(), 0u,
    D3D11_MAP_WRITE_DISCARD, 0u,
    &msr
));
memcpy(msr.pData, &consts, sizeof(consts));
mContext->Unmap(mConstantBuffer.Get(), 0u);

//绑定 常量缓冲区到指定槽 起始槽索引 + 数组数量 + 数组地址
mContext->VSSetConstantBuffers(1u, 1u, mConstantBuffer.GetAddressOf());
```

### InputLayout

```c++
// 定义
ComPtr<ID3D11InputLayout> mInputLayout;
std::vector<D3D11_INPUT_ELEMENT_DESC> layout = 
{
    // 12 = (32 + 32 + 32) / 8 = FLOAT3 * 4 BYTE
    //语义 + 语义索引 + 变量格式 + 输入槽 + 变量偏移 + 是否支持实例化技术 + 实例化技术
    { "POSITION", 0, DXGI_FORMAT_R32G32B32_FLOAT, 0, 0, D3D11_INPUT_PER_VERTEX_DATA, 0 },
    { "COLOR", 0, DXGI_FORMAT_R32G32B32A32_FLOAT, 0, 12, D3D11_INPUT_PER_VERTEX_DATA, 0 }
};
```

### Shader

```c++
//定义
std::unordered_map<std::string, ComPtr<ID3DBlob>> mShaders;

//加载编译
mShaders["testVS"] = D3DUtil::CompileShader(L"Shaders\\color.hlsl", nullptr, "VS", "vs_5_0");
mShaders["testPS"] = D3DUtil::CompileShader(L"Shaders\\color.hlsl", nullptr, "PS", "ps_5_0");
```



### VertexBuffers

```C++
// 定义
struct SimpleVertex
{
	DirectX::XMFLOAT3 Pos;
	DirectX::XMFLOAT4 Color;
	SimpleVertex(DirectX::XMFLOAT3& pos, DirectX::XMFLOAT4& color) 
		: Pos(pos), Color(color)
	{}
};

//创建缓冲区
D3D11_BUFFER_DESC bufferDescription;
ZeroMemory(&bufferDescription, sizeof(bufferDescription));
bufferDescription.Usage = D3D11_USAGE_DEFAULT;//默认堆
bufferDescription.ByteWidth = sizeof(SimpleVertex) * 3;//缓冲区大小
bufferDescription.BindFlags = D3D11_BIND_VERTEX_BUFFER;//缓冲区用途
bufferDescription.CPUAccessFlags = 0;
D3D11_SUBRESOURCE_DATA InitData;
ZeroMemory(&InitData, sizeof(InitData));
InitData.pSysMem = vertices;

//绑定顶点缓冲
UINT stride = sizeof(SimpleVertex);
UINT offset = 0;//可使用缓冲区的部分数据 [stride * i + offset, stride * i + stride]
mDevice->CreateBuffer(&bufferDescription, &InitData, &mVertexBuffer);
//绑定缓冲数组 起始槽索引 + 数组数量 + 数组地址
mContext->IASetVertexBuffers(0u, 1u, mVertexBuffer.GetAddressOf(), &stride, &offset);
```

### IndexBuffers

```c++
// 定义
ComPtr<ID3D11Buffer> mIndexBuffer;
// 创建缓冲区
auto indices = mesh.Indices32;
D3D11_BUFFER_DESC ibd = {};
ibd.BindFlags = D3D11_BIND_INDEX_BUFFER;
ibd.Usage = D3D11_USAGE_DEFAULT;
ibd.CPUAccessFlags = 0u;
ibd.MiscFlags = 0u;
ibd.ByteWidth = UINT(indices.size() * sizeof(unsigned short));
ibd.StructureByteStride = sizeof(unsigned short);
D3D11_SUBRESOURCE_DATA isd = {};
isd.pSysMem = indices.data();
mDevice->CreateBuffer(&ibd, &isd, &mIndexBuffer);
// 绑定索引
mContext->IASetIndexBuffer(mIndexBuffer.Get(), DXGI_FORMAT_R16_UINT, 0u);
```

### Texture

```c++
// create texture resource
D3D11_TEXTURE2D_DESC textureDesc = {};
textureDesc.Width = s.GetWidth();
textureDesc.Height = s.GetHeight();
textureDesc.MipLevels = 0;
textureDesc.ArraySize = 1;
textureDesc.Format = DXGI_FORMAT_B8G8R8A8_UNORM;
textureDesc.SampleDesc.Count = 1;
textureDesc.SampleDesc.Quality = 0;
textureDesc.Usage = D3D11_USAGE_DEFAULT;
textureDesc.BindFlags = D3D11_BIND_SHADER_RESOURCE | D3D11_BIND_RENDER_TARGET;
textureDesc.CPUAccessFlags = 0;
textureDesc.MiscFlags = D3D11_RESOURCE_MISC_GENERATE_MIPS;
wrl::ComPtr<ID3D11Texture2D> pTexture;
GFX_THROW_INFO( GetDevice( gfx )->CreateTexture2D(
    &textureDesc,nullptr,&pTexture
) );

// write image data into top mip level
GetContext( gfx )->UpdateSubresource(
    pTexture.Get(),0u,nullptr,s.GetBufferPtrConst(),s.GetWidth() * sizeof( Surface::Color ),0u
);

// create the resource view on the texture
D3D11_SHADER_RESOURCE_VIEW_DESC srvDesc = {};
srvDesc.Format = textureDesc.Format;
srvDesc.ViewDimension = D3D11_SRV_DIMENSION_TEXTURE2D;
srvDesc.Texture2D.MostDetailedMip = 0;
srvDesc.Texture2D.MipLevels = -1;
GFX_THROW_INFO( GetDevice( gfx )->CreateShaderResourceView(
    pTexture.Get(),&srvDesc,&pTextureView
) );
// generate the mip chain using the gpu rendering pipeline
GetContext( gfx )->GenerateMips( pTextureView.Get() );

GFX_THROW_INFO_ONLY( GetContext( gfx )->PSSetShaderResources( slot,1u,pTextureView.GetAddressOf() ) );
```





### ViewPort

```c++
// 设置视窗 原点在窗口中心
D3D11_VIEWPORT vp;
vp.TopLeftX = 0;
vp.TopLeftY = 0;
vp.Width = width;
vp.Height = height;
vp.MinDepth = 0.0f;
vp.MaxDepth = 1.0f;
//光栅化阶段（Rasterize Stage）设置视窗 
mContext->RSSetViewports(1, &vp);//1 数组数量 &vp 数组地址
```

### 流程

```c++
//图元装配 顶点 + 索引 + 绘制方式
mContext->IASetVertexBuffers(0u, 1u, mVertexBuffer.GetAddressOf(), &stride, &offset);
mContext->IASetVertexBuffers(0u, 1u, mVertexBuffer.GetAddressOf(), &stride, &offset);
mContext->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);

//顶点着色阶段 改变顶点位置
for(UINT i = 0; i < numVertices; ++i)
  outputVertex[i] = VertexShader( inputVertex[i] );// 顶点着色器

//曲面细分 LOD技术

//几何着色 从输入图元生成新的图元或销毁

//几何裁减 
  
```



# DirectX 12


## Header

```c++

#include <d3d12.h> // header
#include <d3dcompiler.h> //struct class ...
#include <dxgi.h> //factory

#pragma comment(lib,"d3d12.lib")
#pragma comment(lib,"d3dcompiler.lib")
#pragma comment(lib, "dxgi.lib")

//#define IID_PPV_ARGS
#ifndef ThrowIfFailed // 异常宏
#define ThrowIfFailed(x)                                              \
{                                                                     \
    HRESULT hr__ = (x);                                               \
    if(FAILED(hr__)) { throw D3DException(hr__, L#x, __FILE__, __LINE__); } \
}
#endif
```



## MakeDevice

```c++


CreateDXGIFactory(IID_PPV_ARGS(&pdxgiFactory));
```

## MakeSwapChain

