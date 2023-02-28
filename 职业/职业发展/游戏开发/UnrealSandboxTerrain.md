# UnrealSandboxTerrain

## Actor

- ASandboxTerrainController
  - SpawnInitialZone
    - 生成初始网格数据
    - TTerrainData
    - TVoxelDataInfoPtr
  - BatchSpawnZone
  - GenerateMesh
  - AddTerrainZone
    - UTerrainZoneComponent
    - UVoxelMeshComponent
  - PerformCheckArea

## Component

- UTerrainZoneComponent
- UVoxelMeshComponent
- UTerrainGeneratorComponent
- UTerrainClientComponent

## Loader

- TTerrainAreaHelper
- TTerrainLoadHelper

## Data

# UnrealSandboxToolkit

## Actor

- ASandboxEnvironment
- ASandboxLevelController
- ASandboxPlayerController
- ASandboxObject

# DarkLithosphere

## Actor

ATerrainController : public ASandboxTerrainController

## Component

UMainTerrainGeneratorComponent : public UTerrainGeneratorComponent

# 技术

柏林噪音

超体素 The Transvoxel Algorithm