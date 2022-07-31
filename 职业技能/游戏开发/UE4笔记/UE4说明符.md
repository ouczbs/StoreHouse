# [类](https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/GameplayArchitecture/Classes/)

## 命名规范

| 前缀 |                             说明                             |
| :--: | :----------------------------------------------------------: |
| `A` | 从 *可生成的* 游戏性对象的基础类进行延伸。它们是 Actor，可直接生成到世界场景中。 |
| `U` | 从所有游戏性对象的基础类进行延伸。它们无法被实例到世界场景中，必须从属于 Actor。总体而言，它们是与 [组件](https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/ProgrammingWithCPP/UnrealArchitecture/Actors/Components) 相似的对象。 |

## 类声明

```c++
//类说明符 and 元数据
UCLASS([specifier, specifier, ...], [meta(key=value, key=value, ...)])
class ClassName : public ParentName
{
    GENERATED_BODY()
    //GENERATED_BODY() 宏必须被放置在类体的最前方
}
```



# 函数

## 函数声明

```c++
//函数说明符 and 元数据
UFUNCTION([specifier1=setting1, specifier2, ...], [meta(key1="value1", key2, ...)])
ReturnType FunctionName([Parameter1, Parameter2, ..., ParameterN1=DefaultValueN1, ParameterN2=DefaultValueN2]) [const];
```

## 函数说明符

|            说明符             |                             效果                             |
| :---------------------------: | :----------------------------------------------------------: |
|      `BlueprintCallable`      |             此函数可在蓝图或关卡蓝图图表中执行。             |
| `BlueprintImplementableEvent` |             此函数可在蓝图或关卡蓝图图表中实现。             |
|    `BlueprintNativeEvent`     | 此函数旨在被蓝图覆盖掉，但是也具有默认原生实现。用于声明名称与主函数相同的附加函数，但是末尾添加了`*Implementation`，是写入代码的位置。如果未找到任何蓝图覆盖，该自动生成的代码将调用 `*Implementation` 方法。 |

# 属性

## 属性声明

```c++
//属性说明符 and 元数据
EditAnywhere
VisibleAnywhere
UPROPERTY([specifier, specifier, ...], [meta(key=value, key=value, ...)])
Type VariableName;
```

# 结构体

