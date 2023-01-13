# 解包

## 项目链接

- [AssertSudio](https://gitlab.com/RazTools/Studio/)
  
  - 导入FBXSDK 引用路径 和 库路径
  
  - ```shell
    $(FBXSDK)\include;
    $(FBXSDK)\lib\vs2019\$(Platform)\$(Configuration)\;
    
    $(FBXSDK)\include;%(AdditionalIncludeDirectories)
    $(FBXSDK)\lib\vs2019\$(Platform)\$(Configuration)\;%(AdditionalLibraryDirectories)
    ```
  
- [GenshinData](https://github.com/Dimbreath/GenshinData)
  - 游戏表格数据解析
  - AssertSudio 似乎不能解密 表格数据，不知道作者怎么实现的

## [AssertSudio](https://gitlab.com/RazTools/Studio/)

### 类

- SerializedFile
  - Files
    - fullName
    - fileName
  - Assert
    - Objects
    - ObjectsDict
  - SerializedFileHeader
    - 