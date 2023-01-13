# 学习资料

[文档](https://docs.unrealengine.com/4.27/zh-CN/ProgrammingAndScripting/Slate/Architecture/)

# 结构

## 控件

子槽 即为子控件接口

### **叶控件** 

- 不带子槽的控件

- 可绑定静态数据 

- 可绑定函数委托

### 面板

- 子槽数量为动态的控件

### 合成控件

- 子槽显式命名、数量固定的控件

## 布局

1. 通道 1：**Cache Desired Size** - 相关函数为 **SWidget::CacheDesiredSize** 和 **SWidget::ComputeDesiredSize**
2. 通道 2：**ArrangeChildren** - 相关函数为 **SWidget::ArrangeChildren**

## SWidget 剖析

定义 Slate 中 SWidget 行为的关键函数为：

- ComputeDesiredSize() - 负责所需的大小。
- ArrangeChildren() - 负责父项分配区域中的子项排列。
- OnPaint() - 负责外观。
- Event handlers - 它们的形式为 OnSomething。Slate 将在特定时机在控件上调用这些函数。

## Window

布局作用于Window的 Tab ， 通过TabId 绑定

注册  Tab 创建时的回调，返回创建的Tab内容

- Tab1
  - widget

- Tab2
  - 

# 语法

## 控件

### 创建

```
SLATE_BEGIN_ARGS( SSubMenuButton )
    : _ShouldAppearHovered( false )
    {}
    /** 将显示在按钮上的标签 */
    SLATE_ATTRIBUTE( FString, Label )
    /** 单击按钮时调用 */
    SLATE_EVENT( FOnClicked, OnClicked )
SLATE_END_ARGS()

// 为静态网格体添加一个新的部分
SSubMenuButton
.Label("")
.OnClicked(this ,&...);

void Construct(const FArguments& InArgs){ 
	InArgs.Label
	InArgs.OnClicked
};
可变容器 用 + 添加 任意 个 slot
其他控件只能包含一个[子控件]
```

## 风格

### 定义

```
Set( "ToolBar.Background", FSlateBoxBrush( TEXT("Common/GroupBorder"), FMargin(4.0f/16.0f) ) );
.BorderImage( FEditorStyle::GetBrush( "ToolBar.Background" ) )
```

# 界面创建

## 



# **实例** 

## STextBlock 

## **SEditableText** 

- **OnTextChanged** 
- OnTextCommitted

# SListView

## class

### ToolEditor

```c++
class FAssetEditorToolkit{
public:
    //override
    virtual void RegisterTabSpawners(const TSharedRef<class FTabManager>& TabManager) override{
        //Register your Tab onSpawn in layout
        InTabManager->RegisterTabSpawner(DataTableTabId, FOnSpawnTab::CreateSP(this, 	&FStructTableEditor::CreateAndRegisterDataTableTab))
		.SetDisplayName(LOCTEXT("DataTableDetailsTab", "Data Table Details"))
		.SetGroup(WorkspaceMenuCategory.ToSharedRef());
    }
	virtual void UnregisterTabSpawners(const TSharedRef<class FTabManager>& TabManager) override{
        //unRegister your Tab onSpawn in layout
    }
    //visit interface
    virtual void InitEditor( const EToolkitMode::Type Mode, const TSharedPtr< class IToolkitHost >& InitToolkitHost, UStructTable* Table ){
        //Make Window Layout with your Tabs
        TSharedRef<FTabManager::FLayout> StandaloneDefaultLayout = FTabManager::NewLayout( "Standalone_DataTableEditor_Layout_v6" );
        //Make Window
        FAssetEditorToolkit::InitAssetEditor( Mode, InitToolkitHost, FStructTableEditorModule::DataTableEditorAppIdentifier, StandaloneDefaultLayout, bCreateDefaultStandaloneMenu, bCreateDefaultToolbar, Table );
    }
    
    TSharedRef<SDockTab> SpawnTab_DataTable( const FSpawnTabArgs& Args )
    {
        return SNew(SDockTab)
            .Icon( FEditorStyle::GetBrush("StructTableEditor.Tabs.Properties") )
            .Label( LOCTEXT("StructTableTitle", "Data Table") )
            .TabColorScale( GetTabColorScale() )
            [
                SNew(SBorder)
                .Padding(2)
                .BorderImage( FEditorStyle::GetBrush( "ToolPanel.GroupBorder" ) )
                [
                    DataTableTabWidget.ToSharedRef()
                ]
            ];
    }
    TSharedRef<SVerticalBox> FStructTableEditor::CreateContentBox()
    {
        TSharedRef<SScrollBar> HorizontalScrollBar = SNew(SScrollBar)
            .Orientation(Orient_Horizontal)
            .Thickness(FVector2D(12.0f, 12.0f));

        TSharedRef<SScrollBar> VerticalScrollBar = SNew(SScrollBar)
            .Orientation(Orient_Vertical)
            .Thickness(FVector2D(12.0f, 12.0f));
		//need to create at here
        ColumnNamesHeaderRow = SNew(SHeaderRow);

        CellsListView = SNew(SListView<FStructTableEditorRowListViewDataPtr>)
            .ListItemsSource(&VisibleRows)
            .HeaderRow(ColumnNamesHeaderRow)
            .OnGenerateRow(this, &FStructTableEditor::MakeRowWidget)
            .OnSelectionChanged(this, &FStructTableEditor::OnRowSelectionChanged)
            .ExternalScrollbar(VerticalScrollBar)
            .ConsumeMouseWheel(EConsumeMouseWheel::Always)
            .SelectionMode(ESelectionMode::Single)
            .AllowOverscroll(EAllowOverscroll::No);

        LoadLayoutData();
        RefreshCachedDataTable();

        return SNew(SVerticalBox)
            + SVerticalBox::Slot()
            .AutoHeight()
            [
                SNew(SHorizontalBox)
                + SHorizontalBox::Slot()
                [
                    SAssignNew(SearchBoxWidget, SSearchBox)
                    .InitialText(this, &FStructTableEditor::GetFilterText)
                    .OnTextChanged(this, &FStructTableEditor::OnFilterTextChanged)
                    .OnTextCommitted(this, &FStructTableEditor::OnFilterTextCommitted)
                ]
            ]
            +SVerticalBox::Slot()
            [
                SNew(SHorizontalBox)
                +SHorizontalBox::Slot()
                [
                    SNew(SScrollBox)
                    .Orientation(Orient_Horizontal)
                    .ExternalScrollbar(HorizontalScrollBar)
                    +SScrollBox::Slot()
                    [
                        CellsListView.ToSharedRef()
                    ]
                ]
                +SHorizontalBox::Slot()
                .AutoWidth()
                [
                    VerticalScrollBar
                ]
            ]
            +SVerticalBox::Slot()
            .AutoHeight()
            [
                SNew(SHorizontalBox)
                +SHorizontalBox::Slot()
                [
                    HorizontalScrollBar
                ]
            ];
    }
}
```



### SListView

```C++
//只能放指针啊
template <typename ItemType>
class SListView : public STableViewBase, TListTypeTraits<ItemType>::SerializerType, public ITypedTableView< ItemType >
{
protected:
    const TArray<ItemType>* ItemsSource;
    HeaderRow
public:
    virtual TSharedRef<ITableRow> GenerateNewWidget(ItemType InItem){
        if ( OnGenerateRow.IsBound() )
		{
			return OnGenerateRow.Execute( InItem, SharedThis(this) );
		}
        // The programmer did not provide an OnGenerateRow() handler; let them know.
        TSharedRef< STableRow<ItemType> > NewListItemWidget = 
            SNew( STableRow<ItemType>, SharedThis(this) )
            .Content()
            [
            	SNew(STextBlock) .Text( NSLOCTEXT("SListView", "BrokenUIMessage", "OnGenerateWidget() not assigned.") )
        	];
        return NewListItemWidget;
    }
    virtual void Private_SignalSelectionChanged(ESelectInfo::Type SelectInfo) override
	{
		if( OnSelectionChanged.IsBound() )
		{
			OnSelectionChanged.ExecuteIfBound(SelectedItem, SelectInfo );
		}
	}
}

```



### SMultiColumnTableRow

```c++
/*
* A widget to represent a row in a Data Table Editor widget. This widget allows us to do things like right-click
* and take actions on a particular row of a Data Table.
*/
template<typename ItemType>
class SMultiColumnTableRow : public STableRow<ItemType>{
    void Construct(const FTableRowArgs& InArgs, const TSharedRef<STableViewBase>& OwnerTableView)
	{
        // Sign up for notifications about changes to the HeaderRow
		TSharedPtr< SHeaderRow > HeaderRow = OwnerTableView->GetHeaderRow();
		check( HeaderRow.IsValid() );
		HeaderRow->OnColumnsChanged()->AddSP( this, &SMultiColumnTableRow<ItemType>::GenerateColumns );
		// Populate the row with user-generated content
		this->GenerateColumns( HeaderRow.ToSharedRef() );
    }
    void GenerateColumns( const TSharedRef<SHeaderRow>& InColumnHeaders )
	{
		const TIndirectArray<SHeaderRow::FColumn>& Columns = InColumnHeaders->GetColumns();
		const int32 NumColumns = Columns.Num();
		for( int32 ColumnIndex = 0; ColumnIndex < NumColumns; ++ColumnIndex )
		{
            const SHeaderRow::FColumn& Column = Columns[ColumnIndex];
            GenerateWidgetForColumn(Column.ColumnId);
        }
    }
    virtual TSharedRef<SWidget> GenerateWidgetForColumn(const FName& ColumnName)
    {
        return SNullWidget::NullWidget;
    }
}
```

### STableViewBase

```c++
class SLATE_API STableViewBase : public SCompoundWidget, public IScrollableWidget
{
protected:
    /** Column headers that describe which columns this list shows */
	TSharedPtr< SHeaderRow > HeaderRow;
    
public:
	TSharedPtr<SHeaderRow> GetHeaderRow() const;
    
    
}
```

