# 委托

- 每种委托对应一个类

  - 似乎还不存在基类，可以统一管理
  - ZBaseDelgate

- 声明委托（委托签名）：使用宏声明和定义一个委托类，表明委托可接受的函数签名

  - `DECLARE_DELEGATE(FTypeDelegate);`
  - `typedef TBaseDelegate<int, FString> MyDelegateType;`

- 创建委托：在需要执行委托的类中声明一个委托成员变量

  - `FTypeDelegate FObjectDelegate`

- 执行委托：执行委托，会触发绑定的函数

  - ```
    FObjectDelegate.ExecuteIfBound();
    FObjectDelegate.ExecuteIfBound(1);
    FObjectDelegate.ExecuteIfBound(1, 0.5f);
    ```

- 绑定委托：将委托执行时触发的对象成员函数绑定到这个委托上

  - ```
    //假设为了触发FMyObject2中的MyFunction函数。
    FObjectDelegate.BindRaw(this,&FMyObject2::MyFunction);//this为非UObject对象，最好不用，使用BindSP代替
    FObjectDelegate.BindUObject(this,&FMyObject2::MyFunction);//this为UObject对象
    FObjectDelegate.BindUFunction(this,FName("MyFunction"));//最好不用，使用BindUObject代替
    FObjectDelegate.BindSP(MyObject2Ptr,&FMyObject2::MyFunction);//MyObject2Ptr为共享指针
    FObjectDelegate.BindStatic(&FMyObject2::MyFunction);//MyFunction为static函数
    FObjectDelegate.BindLambda([](){});//绑定一个Lambda函数
    
    FObjectDelegate.UnBind();//解除绑定
    
    ```

# 委托分类

## 单播委托

一对一

## 多播委托

一对多

## 动态委托

可用于蓝图

## 事件

限定类的多播

# 不定参数

	template <typename Functor, typename FuncType>
	struct TFunctionRefCaller;
	template <typename Functor, typename Ret, typename... ParamTypes>
	struct TFunctionRefCaller<Functor, Ret (ParamTypes...)>
	{
		static Ret Call(void* Obj, ParamTypes&... Params)
		{
			return Invoke(*(Functor*)Obj, Forward<ParamTypes>(Params)...);
		}
	};
	
	template <typename Functor, typename... ParamTypes>
	struct TFunctionRefCaller<Functor, void (ParamTypes...)>
	{
		static void Call(void* Obj, ParamTypes&... Params)
		{
			Invoke(*(Functor*)Obj, Forward<ParamTypes>(Params)...);
		}
	};
	
	template <typename FuncType, typename... ArgTypes>
	FORCEINLINE auto Invoke(FuncType&& Func, ArgTypes&&... Args)
		-> decltype(Forward<FuncType>(Func)(Forward<ArgTypes>(Args)...))
	{
		return Forward<FuncType>(Func)(Forward<ArgTypes>(Args)...);
	}

# C++

## 左值

有名字的，可获得地址的值或变量

表达式结束后依然存在的*持久化对象*

## 右值

函数转发会让右值拥有名字

无名的 ， 除左值外的值

指表达式结束时就不再存在的*临时对象*

右值引用 临时地址

看它能不能放在等式左边

move 、forward 转化左值为右值

## 引用

### 左值引用

左值别名 ，指向同一个地址

只能左值赋值

### 右值引用

指向临时变量，直接使用临时对象已经申请的资源

右值引用只能右值赋值

### 通用引用

```
template<typename T>
void f( T&& param){
    
}
f(10);  //10是右值
int x = 10; //
f(x); //x是左值
```

## 模板

1. 编译器并不是把函数模板处理成能够处理任意类的函数

2. 编译器从函数模板通过具体类型来产生不同的函数

3. 编译器会对函数模板进行两次编译

   （1）在声明的位置对模板代码进行编译

   （2）在调用的位置对参数替换后的代码进行编译
