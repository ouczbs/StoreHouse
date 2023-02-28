# Class

## Object

### UVoxelGenerator

```c++
class VOXEL_API UVoxelGenerator : public UObject
{

public:
	//~ Begin UVoxelGenerator Interface
	virtual void ApplyParameters(const TMap<FName, FString>& Parameters);
	virtual TArray<FVoxelGeneratorParameter> GetParameters() const;
	
	virtual TVoxelSharedRef<FVoxelGeneratorInstance> GetInstance(const TMap<FName, FString>& Parameters);
	virtual TVoxelSharedRef<FVoxelGeneratorInstance> GetInstance();

	virtual FVoxelGeneratorOutputs GetGeneratorOutputs() const;
    
	//~ End UVoxelGenerator Interface
	TMap<FName, FString> ApplyParametersInternal(const TMap<FName, FString>& Parameters);
};
```

- UVoxelTransformableGenerator
  - UVoxelGraphGenerator

### UVoxelMeshSpawnerBase



```shell
git log --all -- '**/IVoxelPool.cpp'

```



## Actor

### AChunkActor

```c++
AChunkActor::AChunkActor()
{
	mesh = CreateDefaultSubobject<UProceduralMeshComponent>(FName("GeneratedMesh"));
	RootComponent = mesh;
}
void AChunkActor::Initialize(Chunk* chunk, float scale)
{
	this->chunk = chunk;
	this->scale = scale;
}
void AChunkActor::Update()
{
	chunk->Process();
	TArray<FVector> vertices = chunk->Vertices;
	TArray<int> triangles = chunk->Triangles;
	TArray<FVector> normals = chunk->Normals;
	TArray<FVector2D> UV0;
	TArray<FColor> vertexColors;
	TArray<FProcMeshTangent> tangents;

	mesh->CreateMeshSection(0, vertices, triangles, normals, UV0, vertexColors, tangents, false);
	mesh->SetWorldScale3D(FVector(scale));
}
```

### AVoxelWorld

```c++
class PROCEDURAL_API AVoxelWorld : public AActor
{
	void Update();

	UFUNCTION(BlueprintCallable, Category = "Voxel")
	void SetLevel(float level);

private:
	void CreateWorld();

	int Size;
	float Scale;
	World* world;
	TArray<AChunkActor*> chunks;
};
```



```c++
AVoxelWorld::AVoxelWorld()
{
	Size = 10;
	Scale = 100;
}
void AVoxelWorld::CreateWorld()
{
	this->world = new World(Size);
	chunks.SetNumUninitialized(Size * Size * Size);
	for (int x = 0; x < Size; x++)
	{
		for (int y = 0; y < Size; y++)
		{
			for (int z = 0; z < Size; z++)
			{

				AChunkActor* chunkActor = this->GetWorld()->SpawnActor<AChunkActor>(Chunk::Size * Scale * FVector(x, y, z), FRotator::ZeroRotator);
				Chunk* chunk = world->Chunks[x + Size*y + Size*Size*z];
				FString name = FString::FromInt(x) + ", " + FString::FromInt(y) + ", " + FString::FromInt(z);

				chunkActor->AttachToActor(this, FAttachmentTransformRules(EAttachmentRule::KeepRelative, true));
				chunkActor->SetActorLabel(name);
				chunkActor->Initialize(chunk, Scale);

				chunks[x + Size*y + Size*Size*z] = chunkActor;
			}
		}
	}
}
```



## AGe

## Component

### UProceduralMeshComponent

Chunk ：网格数据

UProceduralMeshComponent ：绘制组件

## DataWorld

### Chunk

```c++
Chunk::Chunk()
{
    // vertices & normals
    vertices = std::vector<FVector>(Size * Size * Size);
    
    for (int x = 0; x < Size; x++) {
        for (int y = 0; y < Size; y++) {
            for (int z = 0; z < Size; z++) {
                vertices[GetIndex(x, y, z)] = FVector(x, y, z);
            }
        }
    }

    // others
    Level = 0;
}


void Chunk::Process()
{
    /**
     * Initialize
     */
    trianglesCount = 0;
    verticesCount = 0;
    int n = Size * Size * Size;
    keepVertices.resize(n);
    vertices.resize(n);
    normals.resize(n);
    /**
     * Polygonize
     */
    for (int x = 0; x < Size - 1; x++) {
        for (int y = 0; y < Size - 1; y++) {
            for (int z = 0; z < Size - 1; z++) {
                int grid[8] = {
                    GetIndex(x, y + 1, z),
                    GetIndex(x + 1, y + 1, z),
                    GetIndex(x + 1, y, z),
                    GetIndex(x, y, z),
                    GetIndex(x, y + 1, z + 1),
                    GetIndex(x + 1, y + 1, z + 1),
                    GetIndex(x + 1, y, z + 1),
                    GetIndex(x, y, z + 1)
                };
                Polygonise(grid);
            }
        }
    }

    // Compute bijection array & vertice & normals
    std::vector<int> bijection(vertices.size());
    int usedVertices = 0;
    for (int i = 0; i < vertices.size(); i++) {
        if (keepVertices[i]) {
            Vertices[usedVertices] = vertices[i];
            Normals[usedVertices] = normals[i];
            bijection[i] = usedVertices;
            usedVertices++;
        }
    }

    // Compute triangles
    int i = 0;
    for (std::forward_list<int>::iterator it = triangles.begin(); it != triangles.end(); it++) {
        Triangles[trianglesCount - 1 - i] = bijection[*it];
        i++;
    }
}
int Chunk::Interpolate(int verticeIndex1, int verticeIndex2)
{
	float value1, value2;
	value1 = GetValue(verticeIndex1);
	value2 = GetValue(verticeIndex2);

	auto got = interpolationDict.find(CreatePair(verticeIndex1, verticeIndex2));

	if (true || got == interpolationDict.end())
	{
		int x;

		if (std::abs(Level - value1) < 0.00001)
		{
			x = verticeIndex1;
		}
		else if (std::abs(Level - value2) < 0.00001)
		{
			x = verticeIndex2;
		}
		else if (std::abs(value1 - value2) < 0.00001)
		{
			x = verticeIndex1;
		}
		else
		{
			x = vertices.size();
			vertices.push_back(vertices[verticeIndex1] + (Level - value1) / (value2 - value1) * (vertices[verticeIndex2] - vertices[verticeIndex1]));
			normals.push_back(FVector::ZeroVector);
			keepVertices.push_back(false);
		}

		interpolationDict.insert({ CreatePair(verticeIndex1, verticeIndex2), x });
		return x;
	}
	else
	{
		return got->second;
	}
}

```

### World

```
class World
{
public:
	World(int size);
	~World();

	const int Size;
	const int ChunkSize;
	float Level;
	std::vector<Chunk*> Chunks;

	void Randomize();
	void Sphere();

	float GetValue(int x, int y, int z);

private:
	std::vector<float> values;
};\


```





