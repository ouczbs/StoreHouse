-   编译系统  
    
    -   UnrealBuildTool UBT，C#  
        -   UE4的自定义工具，来编译UE4的逐个模块并处理依赖等。我们编写的Target.cs，Build.cs都是为这个工具服务的  
            
    
    -   UnrealHeaderTool UHT，C++  
        -   UE4的C++代码解析生成工具，我们在代码里写的那些宏UCLASS等和#include "*.generated.h"都为UHT提供了信息来生成相应的C++反射代码。  
            
    
    -   UBT会先调用UHT会先负责解析一遍C++代码，生成相应其他代码。  
        
    
    -   然后开始调用平台特定的编译工具(VisualStudio,LLVM)来编译各个模块。  
        
    
    -   最后启动Editor或者是Game.