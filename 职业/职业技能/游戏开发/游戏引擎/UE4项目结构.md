-   项目文件目录  
    
    -   Binaries:  
        -   存放编译生成的结果二进制文件。该目录可以gitignore,反正每次都会生成。  
            
    
    -   Config:  
        -   配置文件。  
            
    
    -   Content:  
        -   平常最常用到，所有的资源和蓝图等都放在该目录里。  
            
    
    -   DerivedDataCache：  
        -   “DDC”，存储着引擎针对平台特化后的资源版本。比如同一个图片，针对不同的平台有不同的适合格式，这个时候就可以在不动原始的uasset的基础上，比较轻易的再生成不同格式资源版本。gitignore。  
            
    
    -   Intermediate：  
        -   中间文件（gitignore），存放着一些临时生成的文件。有：  
            
            -   Build的中间文件，.obj和预编译头等  
                
            
            -   UHT预处理生成的.generated.h/.cpp文件  
                
            
            -   VS.vcxproj项目文件，可通过.uproject文件生成编译生成的Shader文件。  
                
            
            -   AssetRegistryCache：Asset Registry系统的缓存文件，Asset Registry可以简单理解为一个索引了所有uasset资源头信息的注册表。CachedAssetRegistry.bin文件也是如此。  
                
    
    -   Plugins  
        -   引擎插件  
            
    
    -   Saved：  
        -   存储自动保存文件，其他配置文件，日志文件，引擎崩溃日志，硬件信息，烘培信息数据等。gitignore  
            
    
    -   Source：  
        -   代码文件。  
            
    
    -   Third Party  
        -   第三方库