# 流程
## 定义 Config

特定于引擎、特定于项目和特定平台的配置

- Game 
- Engine

## 获取

- 读取配置文件 
  - [/Script/ModuleName.ExampleClass]
  - 可以从配置文件查看

- GetMutableDefault 可变默认对象
- GetDefault 不可变默认对象

# 配置说明符

```c++
//定义
UCLASS(Config = Engine , defaultconfig)
UCLASS(Config = Engine , defaultconfig)
class CRASHTOOL_API UCrashToolRuntimeSetting : public UObject
{
	GENERATED_BODY()
public:
	UCrashToolRuntimeSetting(const FObjectInitializer& ObjectInitializer)
	:Super(ObjectInitializer)
	,bEnabled(false)
	,Address("")
	{}
	
	UPROPERTY(EditAnywhere,Config,Category="CrashTool")
	bool bEnabled;
	UPROPERTY(EditAnywhere,Config,Category="CrashTool")
	FString Address;
};
//注册到编辑器
ISettingsModule* SettingsModule = FModuleManager::GetModulePtr<ISettingsModule>("Settings");
SettingsModule->RegisterSettings("Project", "Plugins", "CrashTool",
            LOCTEXT("RuntimeSettingsName", "CrashTool"),
            LOCTEXT("RuntimeSettingsDescription", "Project settings for CrashTool"),
            GetMutableDefault<UCrashToolRuntimeSetting>()
            );
//读取
GConfig->GetBool(TEXT("/Script/CrashWindowsTest.CrashToolRuntimeSetting"), TEXT("bEnabled"), bEnableCrashTool, GEngineIni);
GConfig->GetString(TEXT("/Script/CrashWindowsTest.CrashToolRuntimeSetting"), TEXT("Address"), Address, GEngineIni);
//写入
GConfig->SetBool(TEXT("/Script/CrashWindowsTest.CrashToolRuntimeSetting"), TEXT("bEnabled"), true, GEngineIni);
GConfig->SetString(TEXT("/Script/CrashWindowsTest.CrashToolRuntimeSetting"), TEXT("Address"),"要写入的内容" , GEngineIni)
```

# 继承 **UDeveloperSettings**

```c++
//定义
UCLASS(config = Game, defaultconfig, meta = (DisplayName = "HttpMgrSetting"))
class HTTPDOWNLOADTOOL_API UHttpManagerSetting :public UDeveloperSettings
{
	GENERATED_UCLASS_BODY()
public:
	UPROPERTY(config, EditAnywhere, Category = "Config | HttpManager")
	int32						MaxParallel;//最大的下载并行数
	UPROPERTY(config, EditAnywhere, Category = "Config | HttpManager")
	int32						MaxTryCount;//最大的重连次数
	UPROPERTY(config, EditAnywhere, Category = "Config | HttpManager")
	int32						RequestKBSize;//每个子任务的大小
	UPROPERTY(config, EditAnywhere, Category = "Config | HttpManager")
	float						ResponseTimeout;//请求超时的时间
	UPROPERTY(config, EditAnywhere, Category = "Config | HttpManager")
	FString						CurFilePath; //子文件的缓存位置
};
//使用
const UHttpManagerSetting* Config = GetDefault<UHttpManagerSetting>();
```

# 踩坑

## 自动UClass 无效

目标类在其他模块，还未加载，创建CDO时机过早

## 手动修改CDO的值

GetMutableDefault
