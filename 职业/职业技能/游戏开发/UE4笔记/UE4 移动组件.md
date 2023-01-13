# 类

- UCharacterMovementComponent
  - UPawnMovementComponent
    - UNavMovementComponent v
      - UMovementComponent
        - UActorComponent
- APawn

# 输入

https://www.freesion.com/article/2884235638/

```mermaid
sequenceDiagram
title: 输入
participant Functions as 函数调用
	autonumber
	Functions->>APawn: AddMovementInput
	APawn->>UPawnMovementComponent: AddInputVector
	UPawnMovementComponent->>APawn:Internal_AddMovementInput
	APawn->>APawn:.ControlInputVector
	Functions->>UCharacterMovementComponent:TickComponent
	UCharacterMovementComponent->>UCharacterMovementComponent:ControlledCharacterMove
	note over UCharacterMovementComponent:apply input to acceleration
	UCharacterMovementComponent->>UCharacterMovementComponent:ScaleInputAcceleration
	UCharacterMovementComponent->>UCharacterMovementComponent:PerformMovement
	note over UCharacterMovementComponent:Change Position
	UCharacterMovementComponent->>UCharacterMovementComponent:StartNewPhysics
	UCharacterMovementComponent->>UCharacterMovementComponent:CalcVelocity
	UCharacterMovementComponent->>UCharacterMovementComponent:ApplyRequestedMove
	UCharacterMovementComponent->>UCharacterMovementComponent:ApplyVelocityBraking
	UCharacterMovementComponent->>UCharacterMovementComponent:MoveAlongFloor
	#SafeMoveUpdatedComponent , HandleImpact
	#PerformMovement ApplyAccumulatedForces
	
```

