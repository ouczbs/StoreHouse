# 黑板

# 行为树

# 结构设计

```mermaid
sequenceDiagram
	Functions -->> GWorld : Tick
	GWorld -->> Sensor : Update
	Functions ->> Actor : Tick
	Functions ->> Actor : UpdateStrategy
	Actor ->> BehaviorTreeClass : RunBehavior
	BehaviorTreeClass ->> BlackBoard : GetData
	
	Functions ->> Actor : UpdateRequest
	Functions ->> Actor : UpdateAction
	Functions ->> Actor : UpdateAnim
```



