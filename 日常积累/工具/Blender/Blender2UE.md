# 工作流

## UE4 设置

### Python 插件

打开UE4，设置—>插件，搜索python，启用

### 配置插件

编辑—>项目设置，找到python，是否远程执行打勾

## Blender设置

### 下载Blender-Tool插件

- github：[BlenderTools](https://github.com/EpicGames/BlenderTools)
- 文档: [Send to Unreal](https://epicgames.github.io/BlenderTools/)
- [blender_mmd_tools](https://github.com/sugiany/blender_mmd_tools)

### 配置插件

- 

### Blender 使用

#### 材质属性丢失

导出fbx文件时，材质丢失，材质属性只能解析 原理化BSDF着色器
mmd 工具可一键解析
#### 问题
Blender 某些材质缺少纹理，无法send2ue，需要一一替换消除
UE4插件材质为相同的图片创建了多个纹理，需要资源合并引用
### Tips

#### 只导出选择的物体

如果场景里的物体比较多，每次同步都会把相关合集里的所有物体都同步一遍，多了的话很影响效率，只要在blender中选中物体后按快捷键"/"单独显示，再执行Send to Unreal就会只同步当前显示的物体。

#### 如何让导入的资产默认材质为材质实例



