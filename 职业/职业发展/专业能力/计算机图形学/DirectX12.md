# 设计模式

## 组件对象模型

以接口的形式使用，避免对象创建销毁，隐藏底层细节

```c++
ComPtr<ID3D12RootSignature> mRootSignature;
mRootSignature.Get();
mRootSignature.GetAddressOf();
mRootSignature.Reset()
```

## 命令队列

```c++
Microsoft::WRL::ComPtr<ID3D12CommandQueue> mCommandQueue;
ThrowIfFailed(md3dDevice->CreateCommandQueue(&queueDesc, IID_PPV_ARGS(&mCommandQueue)));

```

# 数据类型

## 对象数据类型

### 格式

`DXGI_FORMAT_((Name|Byte)+_Type)+`

```c++
typedef enum DXGI_FORMAT
{
    DXGI_FORMAT_UNKNOWN	                                = 0,
    DXGI_FORMAT_R32G32B32A32_TYPELESS                   = 1,
    DXGI_FORMAT_R32G32B32A32_FLOAT                      = 2,
    DXGI_FORMAT_R32G32B32A32_UINT                       = 3,
    DXGI_FORMAT_R32G32B32A32_SINT                       = 4,
    //........
    DXGI_FORMAT_SAMPLER_FEEDBACK_MIP_REGION_USED_OPAQUE = 190,
}
```

### 类型

- UNORM
  - 映射到 [ 0 , 1 ]
- SNORM
  - 映射到 [-1 , 1 ]
- UINT
  - 映射到 [ 0 , 255 ]
- SINT
  - 映射到 [ -127, 127 ]

### 纹理数据

- DXGI_FORMAT_UNKNOWN

- DXGI_FORMAT_R32G32B32_FLOAT
- DXGI_FORMAT_R16G16B16A16_UNORM
- DXGI_FORMAT_R8G8_SNORM
- DXGI_FORMAT_R16G16B16A16_TYPELESS

### 深度模板数据

- DXGI_FORMAT_D32_FLOAT_S8X24_UINT
- DXGI_FORMAT_D24_UNORM_S8_UINT

## 资源数据类型

### 资源

连续的内存块

```c++
typedef struct D3D12_RESOURCE_DESC
{
    D3D12_RESOURCE_DIMENSION Dimension;
    UINT64 Alignment;
    UINT64 Width;
    UINT Height;
    UINT16 DepthOrArraySize;
    UINT16 MipLevels;
    DXGI_FORMAT Format;
    DXGI_SAMPLE_DESC SampleDesc;
    D3D12_TEXTURE_LAYOUT Layout;
    D3D12_RESOURCE_FLAGS Flags;
 } 	D3D12_RESOURCE_DESC;
```

### 描述符堆

描述资源类型，指定资源用于渲染流水线的那一个阶段

```c++
typedef struct D3D12_DESCRIPTOR_HEAP_DESC
{
    D3D12_DESCRIPTOR_HEAP_TYPE Type;
    UINT NumDescriptors;
    D3D12_DESCRIPTOR_HEAP_FLAGS Flags;
    UINT NodeMask;
 } 	D3D12_DESCRIPTOR_HEAP_DESC;
```

### 输入布局

```c++
typedef struct D3D12_INPUT_LAYOUT_DESC
{
    _Field_size_full_(NumElements)  const D3D12_INPUT_ELEMENT_DESC *pInputElementDescs;
    UINT NumElements;
} 	D3D12_INPUT_LAYOUT_DESC;
typedef struct D3D12_INPUT_ELEMENT_DESC
{
    LPCSTR SemanticName; //语义
    UINT SemanticIndex; //索引
    DXGI_FORMAT Format; //格式
    UINT InputSlot;
    UINT AlignedByteOffset;
    D3D12_INPUT_CLASSIFICATION InputSlotClass;
    UINT InstanceDataStepRate;
} 	D3D12_INPUT_ELEMENT_DESC;
```



### 视图

```c++
//根据描述符创建对应的视图
typedef struct D3D12_VERTEX_BUFFER_VIEW
{
    D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;
    UINT SizeInBytes;
    UINT StrideInBytes;
} 	D3D12_VERTEX_BUFFER_VIEW;
typedef struct D3D12_INDEX_BUFFER_VIEW
{
  D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;
  UINT SizeInBytes;
  DXGI_FORMAT Format;
} D3D12_INDEX_BUFFER_VIEW;
```

### 视口

```c++
typedef struct D3D12_VIEWPORT
 {
    FLOAT TopLeftX;
    FLOAT TopLeftY;
    FLOAT Width;
    FLOAT Height;
    FLOAT MinDepth;
    FLOAT MaxDepth;
 } 	D3D12_VIEWPORT;
```

## 渲染数据类型

### 图元拓扑

```c++
enum D3D_PRIMITIVE_TOPOLOGY
    {
        D3D_PRIMITIVE_TOPOLOGY_UNDEFINED	= 0,
        D3D_PRIMITIVE_TOPOLOGY_POINTLIST	= 1,
        D3D_PRIMITIVE_TOPOLOGY_LINELIST	= 2,
        D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST	= 3,
    	...
    } 	D3D_PRIMITIVE_TOPOLOGY;
```



# 设计流程

##  文档

### 初始化

![image-20220907195332410](F:\ouczbs\StoreHouse\职业发展\专业能力\计算机图形学\assets\image-20220907195332410.png)

### 渲染流水线

![image-20220909090717371](F:\ouczbs\StoreHouse\职业发展\专业能力\计算机图形学\assets\image-20220909090717371.png)



## 概念

### 命令与多线程

Directx12是多线程。命令列表也是一种发挥Direct3D多线程优势的途径。我们可以创建N个线程，每个线程分别构建一个命令列表来绘制1/n的场景物体。以下是多线程环境使用命令列表注意的问题：

1. 命令列表并非自由线程对象。也就是说，多线程既不能同时共享相同的命令列表，也不能同时调用同一命令列表的方法。
2. 命令分配器也不是线程自由对象。也就是说，多线程既不能同时共享相同的命令分配器，也不能同时调用同一命令分配器的方法。
3. 命令队列是线程自由对象，所以多线程可以同时访问同一命令队列，也能够同时调用它的方法。特别是每个线程都能同时向命令队列提交他们自己所生成的命令列表。 
4. 出于性能的原因，应用程序必须在初始化期间，指出用于并行记录命令的命令列表最大数量

### 命令列表

由GPU设备创建 命令内存分配器、命令列表、命令队列

- 命令内存分配器
  - 存储命令到指定内存区域
  - 需要重复使用
- 命令列表
  - 在指定内存上存储与创建Gpu命令
  - 循环使用需先重置内存空间（命令列表未执行的命令保留），命令列表
  - 用于批量将命令转移到命令队列
- 命令队列
  - 从命令列表批量添加指令并执行
  - 在命令列表尾部添加围栏信号命令
  - 创建交换链

```c++
void D3DApp::CreateCommandObjects()
{
	D3D12_COMMAND_QUEUE_DESC queueDesc = {};
	queueDesc.Type = D3D12_COMMAND_LIST_TYPE_DIRECT;
	queueDesc.Flags = D3D12_COMMAND_QUEUE_FLAG_NONE;
	ThrowIfFailed(md3dDevice->CreateCommandQueue(&queueDesc, IID_PPV_ARGS(&mCommandQueue)));

	ThrowIfFailed(md3dDevice->CreateCommandAllocator(
		D3D12_COMMAND_LIST_TYPE_DIRECT,
		IID_PPV_ARGS(mDirectCmdListAlloc.GetAddressOf())));

	ThrowIfFailed(md3dDevice->CreateCommandList(
		0,
		D3D12_COMMAND_LIST_TYPE_DIRECT,
		mDirectCmdListAlloc.Get(), // Associated command allocator
		nullptr,                   // Initial PipelineStateObject
		IID_PPV_ARGS(mCommandList.GetAddressOf())));

	// Start off in a closed state.  This is because the first time we refer 
	// to the command list we will Reset it, and it needs to be closed before
	// calling Reset.
	mCommandList->Close();
}

void D3DApp::Draw(){
    // Reuse the memory associated with command recording.
    // We can only reset when the associated command lists have finished execution on the GPU.
	ThrowIfFailed(mDirectCmdListAlloc->Reset());
	// A command list can be reset after it has been added to the command queue via ExecuteCommandList.
    // Reusing the command list reuses memory.
    ThrowIfFailed(mCommandList->Reset(mDirectCmdListAlloc.Get(), mPSO.Get())); 
    
    BuildDescriptorHeaps();
	BuildConstantBuffers();
    BuildRootSignature();
    BuildShadersAndInputLayout();
    BuildBoxGeometry();
    BuildPSO();

    // Execute the initialization commands.
    ThrowIfFailed(mCommandList->Close());
	ID3D12CommandList* cmdsLists[] = { mCommandList.Get() };
	mCommandQueue->ExecuteCommandLists(_countof(cmdsLists), cmdsLists);

    // Wait until initialization is complete.
    FlushCommandQueue();
}

void D3DApp::FlushCommandQueue()
{
	// Advance the fence value to mark commands up to this fence point.
    mCurrentFence++;

    // Add an instruction to the command queue to set a new fence point.  Because we 
	// are on the GPU timeline, the new fence point won't be set until the GPU finishes
	// processing all the commands prior to this Signal().
    ThrowIfFailed(mCommandQueue->Signal(mFence.Get(), mCurrentFence));

	// Wait until the GPU has completed commands up to this fence point.
    if(mFence->GetCompletedValue() < mCurrentFence)
	{
		HANDLE eventHandle = CreateEventEx(nullptr, false, false, EVENT_ALL_ACCESS);

        // Fire event when GPU hits current fence.  
        ThrowIfFailed(mFence->SetEventOnCompletion(mCurrentFence, eventHandle));

        // Wait until the GPU hits current fence event is fired.
		WaitForSingleObject(eventHandle, INFINITE);
        CloseHandle(eventHandle);
	}
}

```

### 资源屏障

```c++
void Draw(){
// Indicate that the back buffer will be used as a render target.
	CommandList->ResourceBarrier(1, 		&CD3DX12_RESOURCE_BARRIER::Transition(RenderTargets[CurrentBackBufferIndex].Get()
        , D3D12_RESOURCE_STATE_PRESENT
        , D3D12_RESOURCE_STATE_RENDER_TARGET));

....
	CommandList->ResourceBarrier(1, 
    &CD3DX12_RESOURCE_BARRIER::Transition(RenderTargets[CurrentBackBufferIndex].Get()
        , D3D12_RESOURCE_STATE_RENDER_TARGET
        , D3D12_RESOURCE_STATE_PRESENT));

}
    为何要将数据复制到“中间资源”然后再到“实际使用的资源”？例如对于上例D3D12而言，不能直接使用“中间”资源作为采样的纹理吗？
    “中间资源”拷贝到“实际使用的资源”这一步为什么要用一个图形命令？直接在CPU上拷贝不行吗？
```

### 交换链

双缓冲交替使用 （两个渲染目标）

```c++
// Resize the swap chain.
ThrowIfFailed(mSwapChain->ResizeBuffers(
    SwapChainBufferCount, 
    mClientWidth, mClientHeight, 
    mBackBufferFormat, 
    DXGI_SWAP_CHAIN_FLAG_ALLOW_MODE_SWITCH));
// swap the back and front buffers
ThrowIfFailed(mSwapChain->Present(0, 0));
```

### 资源和堆

堆是GPU内部的一块内存区域

```c++
ThrowIfFailed(md3dDevice->CreateCommittedResource(
    &CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_READBACK),
    D3D12_HEAP_FLAG_NONE,
    &CD3DX12_RESOURCE_DESC::Buffer(byteSize),
    D3D12_RESOURCE_STATE_COPY_DEST,
    nullptr,
    IID_PPV_ARGS(&mReadBackBuffer)));
```

- 默认堆
  - CPU不需要访问的数据
- 上传堆
  - 数据需要CPU上传
- 回读堆
  - CPU需要读取的数据

### 资源与描述符与视图

资源是普通的内存块

每个描述符都有一种具体类型，此类型指明了资源的具体作用。

创建描述符的最佳时机为初始化期间。由于在此过程中需要执行一些类型的检测和验证工作，所以最好不要在运行时（runtime）才创建描述符

视图具体资源与描述符绑定后得到视图，由视图通过描述符将资源绑定到流水线上

#### 描述符堆

数组形式创建多个同类型的描述符，通过获取首个描述符与偏移量来获取指定位置的描述符，未绑定资源

```c++
void D3DApp::CreateRtvAndDsvDescriptorHeaps()
{
    D3D12_DESCRIPTOR_HEAP_DESC rtvHeapDesc;
    rtvHeapDesc.NumDescriptors = SwapChainBufferCount;
    rtvHeapDesc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_RTV;
    rtvHeapDesc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_NONE;
	rtvHeapDesc.NodeMask = 0;
    ThrowIfFailed(md3dDevice->CreateDescriptorHeap(
        &rtvHeapDesc, IID_PPV_ARGS(mRtvHeap.GetAddressOf())));


    D3D12_DESCRIPTOR_HEAP_DESC dsvHeapDesc;
    dsvHeapDesc.NumDescriptors = 1;
    dsvHeapDesc.Type = D3D12_DESCRIPTOR_HEAP_TYPE_DSV;
    dsvHeapDesc.Flags = D3D12_DESCRIPTOR_HEAP_FLAG_NONE;
	dsvHeapDesc.NodeMask = 0;
    ThrowIfFailed(md3dDevice->CreateDescriptorHeap(
        &dsvHeapDesc, IID_PPV_ARGS(mDsvHeap.GetAddressOf())));
}
```

#### 描述符与视图

```c++
CD3DX12_CPU_DESCRIPTOR_HANDLE(
		mRtvHeap->GetCPUDescriptorHandleForHeapStart(),
		mCurrBackBuffer,
		mRtvDescriptorSize);

CD3DX12_CPU_DESCRIPTOR_HANDLE rtvHeapHandle(mRtvHeap->GetCPUDescriptorHandleForHeapStart());
	for (UINT i = 0; i < SwapChainBufferCount; i++)
	{
		ThrowIfFailed(mSwapChain->GetBuffer(i, IID_PPV_ARGS(&mSwapChainBuffer[i])));
		md3dDevice->CreateRenderTargetView(mSwapChainBuffer[i].Get(), nullptr, rtvHeapHandle);
		rtvHeapHandle.Offset(1, mRtvDescriptorSize);
	}
```

### 资源转换

```c++
    // Transition the resource from its initial state to be used as a depth buffer.
mCommandList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(mDepthStencilBuffer.Get(),
		D3D12_RESOURCE_STATE_COMMON, D3D12_RESOURCE_STATE_DEPTH_WRITE));
```



### 资源驻留

控制资源在GPU内存中的去留

```c++
HRESULT ID3D12Device::MakeResident(  UINT         NumObjects,  ID3D12Pageable *const *ppObjects);
HRESULT ID3D12Device::Evict(  UINT         NumObjects,  ID3D12Pageable *const *ppObjects);

```

### 渲染管线

#### 输入装配器

顶点 + 索引 + 装配规则 = 图元

所有图元堆积 = 模型

将顶点资源按照索引资源和指定规则装配成图形资源

在用户通过命令列表（command list）修改图元拓扑之前，所有的绘制调用都会沿用当前设置的图元拓扑方式。

```c++
	mCommandList->IASetVertexBuffers(0, 1, &mBoxGeo->VertexBufferView());
	mCommandList->IASetIndexBuffer(&mBoxGeo->IndexBufferView());
    mCommandList->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
```

#### 顶点着色器

旧顶点 =  新顶点（颜色变化、位置变化等）

实现各种特效、空间变换

- 模型空间到世界空间
- 世界空间到投影剪裁空间（裁剪）
- 投影剪裁空间到规格化设备空间（光栅化）

```c++
//GPU代码
for(UINT i = 0; i < numVertices; ++i)
  outputVertex[i] = VertexShader( inputVertex[i] );
```

#### 曲面细分

实现LOD机制，默认以低细节处理，依据离相机的距离决定是否添加细节

处理动画与物理模拟低模

渲染需要高模

#### 几何着色

完整图元 = 新完整图元

点、线扩展为四边形

#### 裁剪

#### 光栅化

- 视口变换

- 背面剔除
- 顶点插值

#### 像素着色

#### 输出合并

### 缓冲区

一种特殊资源，缓冲区的结构比纹理更为简单：既非多维资源，也不支持mipmap、过滤器以及多重采样等技术。当需要向GPU提供如顶点这类数据元素所构成的数组时，我们便会使用缓冲区。

#### 顶点缓冲区

```c++
typedef struct D3D12_VERTEX_BUFFER_VIEW
{
    D3D12_GPU_VIRTUAL_ADDRESS BufferLocation;
    UINT SizeInBytes;
    UINT StrideInBytes;
} 	D3D12_VERTEX_BUFFER_VIEW;
void ID3D12GraphicsCommandList::IASetVertexBuffers(
  UINT StartSlot,
  UINT NumView,
  const D3D12_VERTEX_BUFFER_VIEW *pViews);
void ID3D12GraphicsCommandList::IASetIndexBuffer(
  UINT StartSlot,
  UINT NumView,
  const D3D12_VERTEX_BUFFER_VIEW *pViews);
```



#### 索引缓冲区



## 流程

### 初始化

- 显示设备
- 命令管理
- 交换链
- 描述符与视图
- 配置图形管线
- 渲染目标

### 渲染目标

和D3D11类似，使用交换链的GetBuffer方法获得buffer，然后使用CreateRenderTargetView创建一个RenderTarget（ID3D12Resource）。

不同的是，D3D12中对于显卡资源有一个Descriptor的概念，它相当于一个“View”，而之前的“RenderTargetView”现在就存在一个RTVHeap（DescriptorHeap）中。

## 代码

### init

```c++
void InitDirect3D(){
    InitDevice();
    CreateCommandObjects();
    CreateSwapChain();
    CreateRtvAndDsvDescriptorHeaps();
}
void InitDevice(){
    //
    ThrowIfFailed(CreateDXGIFactory1(IID_PPV_ARGS(&mdxgiFactory)));
    HRESULT hardwareResult = D3D12CreateDevice(
		nullptr,             // default adapter
		D3D_FEATURE_LEVEL_11_0,
		IID_PPV_ARGS(&md3dDevice));
    
    ThrowIfFailed(md3dDevice->CreateFence(0, D3D12_FENCE_FLAG_NONE,
		IID_PPV_ARGS(&mFence)));
    
    ThrowIfFailed(md3dDevice->CheckFeatureSupport(
		D3D12_FEATURE_MULTISAMPLE_QUALITY_LEVELS,
		&msQualityLevels,
		sizeof(msQualityLevels)));
}
void CreateCommandObjects(){
    ThrowIfFailed(md3dDevice->CreateCommandQueue(&queueDesc, IID_PPV_ARGS(&mCommandQueue)));

	ThrowIfFailed(md3dDevice->CreateCommandAllocator(
		D3D12_COMMAND_LIST_TYPE_DIRECT,
		IID_PPV_ARGS(mDirectCmdListAlloc.GetAddressOf())));

	ThrowIfFailed(md3dDevice->CreateCommandList(
		0,
		D3D12_COMMAND_LIST_TYPE_DIRECT,
		mDirectCmdListAlloc.Get(), // Associated command allocator
		nullptr,                   // Initial PipelineStateObject
		IID_PPV_ARGS(mCommandList.GetAddressOf())));
    mCommandList->Close();
}
void D3DApp::CreateSwapChain()
{
    // Release the previous swapchain we will be recreating.
    mSwapChain.Reset();
    DXGI_SWAP_CHAIN_DESC sd;
	// Note: Swap chain uses queue to perform flush.
    ThrowIfFailed(mdxgiFactory->CreateSwapChain(
		mCommandQueue.Get(),
		&sd, 
		mSwapChain.GetAddressOf()));
}
void D3DApp::CreateRtvAndDsvDescriptorHeaps()
{
    D3D12_DESCRIPTOR_HEAP_DESC rtvHeapDesc;
    ThrowIfFailed(md3dDevice->CreateDescriptorHeap(
        &rtvHeapDesc, IID_PPV_ARGS(mRtvHeap.GetAddressOf())));

    D3D12_DESCRIPTOR_HEAP_DESC dsvHeapDesc;
    ThrowIfFailed(md3dDevice->CreateDescriptorHeap(
        &dsvHeapDesc, IID_PPV_ARGS(mDsvHeap.GetAddressOf())));
}
```

### resize

```c++
void D3DApp::ClearBefore()
{
    // Flush before changing any resources.
	FlushCommandQueue();
    ThrowIfFailed(mCommandList->Reset(mDirectCmdListAlloc.Get(), nullptr));
    // Release the previous resources we will be recreating.
	for (int i = 0; i < SwapChainBufferCount; ++i)
		mSwapChainBuffer[i].Reset();
    mDepthStencilBuffer.Reset();
    	// Resize the swap chain.
    ThrowIfFailed(mSwapChain->ResizeBuffers(
		SwapChainBufferCount, 
		mClientWidth, mClientHeight, 
		mBackBufferFormat, 
		DXGI_SWAP_CHAIN_FLAG_ALLOW_MODE_SWITCH));
	mCurrBackBuffer = 0;
}
void D3DApp::RenderNew()
{
    CD3DX12_CPU_DESCRIPTOR_HANDLE rtvHeapHandle(mRtvHeap->GetCPUDescriptorHandleForHeapStart());
	for (UINT i = 0; i < SwapChainBufferCount; i++)
	{
		ThrowIfFailed(mSwapChain->GetBuffer(i, IID_PPV_ARGS(&mSwapChainBuffer[i])));
		md3dDevice->CreateRenderTargetView(mSwapChainBuffer[i].Get(), nullptr, rtvHeapHandle);
		rtvHeapHandle.Offset(1, mRtvDescriptorSize);
	}
    // Create the depth/stencil buffer and view.
    D3D12_RESOURCE_DESC depthStencilDesc;
    D3D12_CLEAR_VALUE optClear;
    ThrowIfFailed(md3dDevice->CreateCommittedResource(
        &CD3DX12_HEAP_PROPERTIES(D3D12_HEAP_TYPE_DEFAULT),
		D3D12_HEAP_FLAG_NONE,
        &depthStencilDesc,
		D3D12_RESOURCE_STATE_COMMON,
        &optClear,
        IID_PPV_ARGS(mDepthStencilBuffer.GetAddressOf())));
    // Create descriptor to mip level 0 of entire resource using the format of the resource.
    md3dDevice->CreateDepthStencilView(mDepthStencilBuffer.Get(), nullptr, DepthStencilView());

    // Transition the resource from its initial state to be used as a depth buffer.
	mCommandList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(mDepthStencilBuffer.Get(),
		D3D12_RESOURCE_STATE_COMMON, D3D12_RESOURCE_STATE_DEPTH_WRITE));
	
    // Execute the resize commands.
    ThrowIfFailed(mCommandList->Close());
    ID3D12CommandList* cmdsLists[] = { mCommandList.Get() };
    mCommandQueue->ExecuteCommandLists(_countof(cmdsLists), cmdsLists);

	// Wait until resize is complete.
	FlushCommandQueue();
}
void D3DApp::OnResize()
{
	ClearBefore();
    RenderNew();
}
```

### draw

```c++
void BoxApp::Draw(const GameTimer& gt)
{
    // Reuse the memory associated with command recording.
    // We can only reset when the associated command lists have finished execution on the GPU.
    ThrowIfFailed(mDirectCmdListAlloc->Reset());

    // A command list can be reset after it has been added to the command queue via ExecuteCommandList.
    // Reusing the command list reuses memory.
    ThrowIfFailed(mCommandList->Reset(mDirectCmdListAlloc.Get(), mPSO.Get()));

    mCommandList->RSSetViewports(1, &mScreenViewport);
    mCommandList->RSSetScissorRects(1, &mScissorRect);

    // Indicate a state transition on the resource usage.
    mCommandList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(CurrentBackBuffer(),
        D3D12_RESOURCE_STATE_PRESENT, D3D12_RESOURCE_STATE_RENDER_TARGET));

    // Clear the back buffer and depth buffer.
    mCommandList->ClearRenderTargetView(CurrentBackBufferView(), Colors::LightSteelBlue, 0, nullptr);
    mCommandList->ClearDepthStencilView(DepthStencilView(), D3D12_CLEAR_FLAG_DEPTH | D3D12_CLEAR_FLAG_STENCIL, 1.0f, 0, 0, nullptr);

    // Specify the buffers we are going to render to.
    mCommandList->OMSetRenderTargets(1, &CurrentBackBufferView(), true, &DepthStencilView());

    ID3D12DescriptorHeap* descriptorHeaps[] = { mCbvHeap.Get() };
    mCommandList->SetDescriptorHeaps(_countof(descriptorHeaps), descriptorHeaps);

    mCommandList->SetGraphicsRootSignature(mRootSignature.Get());

    mCommandList->IASetVertexBuffers(0, 1, &mBoxGeo->VertexBufferView());
    mCommandList->IASetIndexBuffer(&mBoxGeo->IndexBufferView());
    mCommandList->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);

    mCommandList->SetGraphicsRootDescriptorTable(0, mCbvHeap->GetGPUDescriptorHandleForHeapStart());

    mCommandList->DrawIndexedInstanced(
        mBoxGeo->DrawArgs["box"].IndexCount,
        1, 0, 0, 0);

    // Indicate a state transition on the resource usage.
    mCommandList->ResourceBarrier(1, &CD3DX12_RESOURCE_BARRIER::Transition(CurrentBackBuffer(),
        D3D12_RESOURCE_STATE_RENDER_TARGET, D3D12_RESOURCE_STATE_PRESENT));

    // Done recording commands.
    ThrowIfFailed(mCommandList->Close());

    // Add the command list to the queue for execution.
    ID3D12CommandList* cmdsLists[] = { mCommandList.Get() };
    mCommandQueue->ExecuteCommandLists(_countof(cmdsLists), cmdsLists);

    // swap the back and front buffers
    ThrowIfFailed(mSwapChain->Present(0, 0));
    mCurrBackBuffer = (mCurrBackBuffer + 1) % SwapChainBufferCount;

    // Wait until frame commands are complete.  This waiting is inefficient and is
    // done for simplicity.  Later we will show how to organize our rendering code
    // so we do not have to wait per frame.
    FlushCommandQueue();
}

```

