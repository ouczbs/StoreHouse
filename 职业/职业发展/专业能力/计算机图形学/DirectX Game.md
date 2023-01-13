# 设计模型

## 类设计

### Window

- WinMain

  - 入口函数

  - 负责创建并运行APP实例

  - 捕抓APP错误并输出

- ScriptCommander
  - 命令行模式

### APP

- Game

  - CameraContainer
  - Light

  - hehe1

- Camera

- Model

  - Material
  - Mesh

### Graphics

- BeginFrame
  - clearing shader inputs

## 抽象

- Drawable
  - vector::Technique
    - vector::Step
      - vector::Bindable
      - pointer::RenderQueuePass
        - super::BindingPass
          - super::Pass
            - vector::Sink
            - vector::Source
          - vector::Bindable
        - vector::Job
          - pointer::Drawable
          - pointer::Step
  - Drawable::submit<---size_t::channelFilter
    - Technique::submit<---Drawable,size_t::channelFilter
      - Step::Submit<---Drawable
        - RenderQueuePass::Accept<---Job
          - vector::Job::Add

- Model
  - pointer::Node
    - vector::Node
    - vector::Mesh
      - Super::Drawable
  - vector::Mesh
- CameraIndicator
  - super::Drawable
- Projection
  - Frustum
    - super::Drawable
- BlurOutlineRenderGraph
  - super::RenderGraph
    - vector::Pass
    - vector::Sink
    - vector::Source
  - pointer::CachingPixelConstantBufferEx



