# UE4

```C++
InputComponent->BindAxis()

InputComponent->AddActionBinding
```



# 蓝图

```c++
void UInputDelegateBinding::BindInputDelegates(const UClass* InClass, UInputComponent* InputComponent)
{
	if (SupportsInputDelegate(InClass))
	{
		BindInputDelegates(InClass->GetSuperClass(), InputComponent);

		for(UClass* BindingClass : InputBindingClasses)
		{
			UInputDelegateBinding* BindingObject = CastChecked<UInputDelegateBinding>(
				UBlueprintGeneratedClass::GetDynamicBindingObject(InClass, BindingClass)
				, ECastCheckedType::NullAllowed);
			if (BindingObject)
			{
				BindingObject->BindToInputComponent(InputComponent);
			}
		}
	}
}
```

