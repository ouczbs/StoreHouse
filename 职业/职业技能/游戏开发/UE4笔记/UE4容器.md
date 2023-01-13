# C++

不管数据类型是什么

##  Vector

vector clear 之后，内存没有释放，但在下次分配时会复用该块内存。

vector swap 空数据之后，会释放内存。

## Map

map clear 之后，会释放内存。

map swap 空数据之后，会释放内存。

```
TMap<FName,TMap<FName,FString>>
```

tablename DisplayName1 DisplayName2 ...

```c++
NewTargetPinType.PinCategory = InPinCategory;
NewTargetPinType.PinSubCategory = SelectionPinType.PinSubCategory;
NewTargetPinType.PinSubCategoryObject = SelectionPinType.PinSubCategoryObject;

FStructureEditorUtils::ChangeVariableType(StructureDetailsSP->GetUserDefinedStruct(), FieldGuid, PinType);
PinCategory = ["bool" , "byte" , "int" , "int64" , "float" , "double" , "name" , "string" , "text" , "struct" , "interface", "object" , "class" , "softobject" , "softclass"];
TWeakObjectPtr<UObject> PinSubCategoryObject; loadobject<>("objectname");
PinValueType{
    TerminalCategory = $PinCategory;
    TerminalSubCategory = $PinSubCategory;
    TerminalSubCategoryObject = $PinSubCategoryObject;
};
softclass.package;
UEnum* Enum = FindObject<UEnum>(ANY_PACKAGE, *Name);
if (!Enum)
{
    Enum = LoadObject<UEnum>(nullptr, *Name);
}
byte.als_maet
ContainerType = None;
FEdGraphPinType(UEdGraphSchema_K2::PC_Boolean, NAME_None, nullptr, EPinContainerType::None, false, FEdGraphTerminalType();
requesttable 测试1 ， 测试2;
ads name,test,sdf
varkeytype bool,byte.sad;
containertype none,array,enum,map,set
varvaluetype bool,sda;

```



vartypes  int int string

nameids a , b

containtype none array set map 

autodefine

type 

name int string ...

start



end

SListView

STableRow

SPinSelector

OnMouseButtonUp

OnMouseButtonDown

OnMouseClick