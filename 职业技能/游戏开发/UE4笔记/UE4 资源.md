# UPackage

一个资源在文件中对应 uasset，在内存中对应为 UPackage

UPackage 就好比一个班级，底下的数据 UObject  就好比学生，对于班级（UPackage）底下的同学（UObject）来说，UPackage 是 UObject 的  Outer。要知道资源自身数据 UObject 的内容，必须先知道 UPackage 才行。

# Asset 

## 文件格式


- File Summary 文件头信息
- Name Table 包中对象的名字表
- Import Table 存放被该包中对象引用的其它包中的对象信息 (路径名和类型)
- Export Table 该包中的对象信息 (路径名和类型)
- Export Objects 所有 Export Table 中对象的实际数据。

# FLinkerLoad

FLinkerLoad 是作为 uasset 和内存 UPackage 的中间桥梁。在加载内容生成 UPackage 的时候，UPackage 会根据名字找到 uasset 文件，由 FLinkerLoad 来负责加载


# 资源加载

## 蓝图资源

`LoadClass<T>()`用来加载蓝图并获取蓝图Class，比如角色蓝图。如果要用蓝图创建对象，必须先通过LoadClass获取class，然后再通过SpawnActor生成对象。

## 非蓝图资源

`LoadObject<T>()`用来加载非蓝图资源，比如动画、贴图、音效等资源；



其中`LoadClass`是将`StaticLoadClass`封装成了模板函数，省略了一些参数，只需要传您关心的参数即可，使用更为方便。

同样`LoadObject`是将`StaticLoadObject`封装成了模板函数。