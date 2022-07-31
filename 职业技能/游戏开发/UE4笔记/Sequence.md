# Sequencer

动画编辑工具

## 概述

- 添加一些骨架网格体
- 添加动画轨迹来驱动这些骨架网格体
- 再加入摄像机轨迹和摄像机剪辑轨迹以提供视角
- 并在多个摄像机间切换。

# 面板

## Sequencer编辑选项

- **允许任何编辑（Allow Any Edits）** - 允许执行任何编辑，其中有些编辑可能会产生轨迹/键或修改默认属性。
- **仅允许Sequencer编辑（Allow Sequencer Edits Only）** - 任何对属性的编辑都将自动创建一个轨迹（如果不存在）和/或一个关键帧。
  - 这些更改将被隔离到你正在处理的特定镜头中，不会泄漏到其他镜头中。
- **仅允许关卡编辑（Allow Level Edits Only）** - 使用此选项时，你将可直接编辑默认属性对象。
  - 当你想要执行会影响所有镜头的全局更改时，详细信息（Details）面板中将禁用已在Sequencer中具有轨迹的属性。

# 关卡序列

动画场景容器

## Actor to Sequence

### Actor

控制场景中的骨骼网格体

- transform
- animation
  - 添加动画片段

# 过场动画

## 摄像机绑定吊车

## 影视摄像机

### **摄像机剪辑轨迹**



## 摄像机绑定轨道

# 轨迹

## 关键帧

```python
mmd2ue4 target
p = [ 0.14 , 14.4 , 3.19 ] = [ 0.14 , 3.19 , 14.4 ]
r = [ 10.3 ,-30.9 , 3.4  ] = [ 10.3 , 3.4  ,-30.9 ]
p , r = [ x , y , z ] , [ x , z , y ]
blender2ue4 error
-x向前 z向上
p = [ 0.14 , 3.19 , 14.4 ] = [ 0.14 ,-3.19 , 14.4 ]
r = [-10.3 , 3.4  ,-30.9 ] = [-10.3 ,-3.4  , 30.9 ]
p , r = [ x , y , z ] , [ x , z , y ]


```



```c++
		

// SavePackageName = '/Game/...'
NewPackage
UPackage* Package = CreatePackage( *SavePackageName);
NewObject
UNodeMappingContainer* MapperClass = NewObject<UNodeMappingContainer>(Package, *SaveAssetName, RF_Public | RF_Standalone);
LoadObject
// save mapper class
FString const PackageName = Package->GetName();
FString const PackageFileName = FPackageName::LongPackageNameToFilename(PackageName, FPackageName::GetAssetPackageExtension());
UPackage::SavePackage(Package, NULL, RF_Standalone, *PackageFileName, GError, nullptr, false, true, SAVE_NoError);


		SaveAssetDialogConfig.DefaultAssetName = DefaultName;
		SaveAssetDialogConfig.ExistingAssetPolicy = ESaveAssetDialogExistingAssetPolicy::AllowButWarn;
		SaveAssetDialogConfig.AssetClassNames.Add(UNodeMappingContainer::StaticClass()->GetFName());

		FContentBrowserModule& ContentBrowserModule = FModuleManager::LoadModuleChecked<FContentBrowserModule>("ContentBrowser");
		FString SaveObjectPath = ContentBrowserModule.Get().CreateModalSaveAssetDialog(SaveAssetDialogConfig);
		if (!SaveObjectPath.IsEmpty())
		{
			const FString SavePackageName = FPackageName::ObjectPathToPackageName(SaveObjectPath);
			const FString SavePackagePath = FPaths::GetPath(SavePackageName);
			const FString SaveAssetName = FPaths::GetBaseFilename(SavePackageName);
			
			// create package and create object
			UPackage* Package = CreatePackage( *SavePackageName);
			UNodeMappingContainer* MapperClass = NewObject<UNodeMappingContainer>(Package, *SaveAssetName, RF_Public | RF_Standalone);
			USkeletalMeshComponent* PreviewMeshComp = PreviewScenePtr.Pin()->GetPreviewMeshComponent();
			USkeletalMesh* PreviewMesh = PreviewMeshComp->SkeletalMesh;
			if (MapperClass && PreviewMesh)
			{
				// update mapping information on the class
				MapperClass->SetSourceAsset(Rig);
				MapperClass->SetTargetAsset(PreviewMesh);

				const TArray<FNode>& Nodes = Rig->GetNodes();
				for (const auto& Node : Nodes)
				{
					FName MappingName = Skeleton.GetRigBoneMapping(Node.Name);
					if (Node.Name != NAME_None && MappingName != NAME_None)
					{
						MapperClass->AddMapping(Node.Name, MappingName);
					}
				}

				// save mapper class
				FString const PackageName = Package->GetName();
				FString const PackageFileName = FPackageName::LongPackageNameToFilename(PackageName, FPackageName::GetAssetPackageExtension());

				UPackage::SavePackage(Package, NULL, RF_Standalone, *PackageFileName, GError, nullptr, false, true, SAVE_NoError);
			}
		}
```





