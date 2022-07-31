# 移动物体

##  Actor

```c++
Actor->SetActorLocation();
AActor::AddActorWorldOffset(); // Actor沿着某个世界坐标系方向移动
AActor::AddActorLocalOffset(); // Actor沿着当前Actor局部坐标系方向移动
//如果使用AddActorWorldOffset或者AddActorLocalOffset移动Character，那么MovementMode必须设置为fly，否则当DeltaLocation较小时，角色会始终往下掉（即使禁用物理模拟）
```

## Pawn

```c++
Controller->MoveTo();
GetWorld()->GetNavigationSystem()->SimpleMoveToLocation(Controller, DestLocation);
APawn->AddMovementInput(FVector WorldDirection, float ScaleValue = 1.0f, bool bForce = false);
```

## Character

```c++
ACharacter->GetCharacterMovement()->Velocity += FVector(5.f, 5.f, 0.f);
void UCharacterMovementComponent::AddImpulse( FVector Impulse, bool bVelocityChange );
//AddImpulse 一般用来做投掷、爆炸、击飞等物理效果。添加的是一个瞬间的力，之后就不需要每帧做处理了。
void UCharacterMovementComponent::AddForce( FVector Force )
```

## Component



```c++
FLatentActionInfo ActionInfo;
ActionInfo.CallbackTarget = this;
UKismetSystemLibrary::MoveComponentTo(TopDownCameraComponent, Location, Rotation, false, false, 1.f, true, EMoveComponentAction::Move, ActionInfo);
// 一般用来移动Actor身上的Component，例如CameraComponent等。支持平滑移动，可以设置移动到目标Location、Rotation过程的时长。
```



# Class





```mermaid
classDiagram
    class UActorComponent
    UActorComponent <|-- UMovementComponent
    class UMovementComponent{
    	定义了基本的移动操作
    	+USceneComponent * UpdatedComponent;
    	+FVector Velocity;
    	+MoveUpdatedComponent(FVector& Delta);
    	+GetMaxSpeed();
    }
    USceneComponent --* UMovementComponent : 成员
    class USceneComponent{
    	包含了基础移动方法
    	+MoveComponent(FVector& Delta);
    	+GetPhysicsVolume();
    }
    UMovementComponent <|-- UNavMovementComponent
    class UNavMovementComponent{
    	实现AI代理移动
    	+FNavAgentProperties NavAgentProps;
    }
    UNavMovementComponent <|-- UPawnMovementComponent
    class UPawnMovementComponent{
    	可接受玩家输入
    	+AddInputVector(FVector);
    }
    UPawnMovementComponent <|-- UCharacterMovementComponent
    class UCharacterMovementComponent{
    	+float Mass;重量
    	+FVector Acceleration;
    	+StartNewPhysics();
    	+PhysWalking();
    }
```

![img](https://img-blog.csdn.net/20171130205337919?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk5OTk4NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# 精确移动

- SetPosition
- SetLocation

# 模拟移动

- Acceleration
- Velocity

