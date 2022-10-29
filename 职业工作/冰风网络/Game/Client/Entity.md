# Class

- Entity
  - 定义
    - 客户端实体 + 服务器通信
    - render UI
      - Visible
      - drawHeadInfo
      - drawName
      - drawOther
        - drawHpBar
      - refreshHeadInfo
    - Buffer
      - addBufferState(U32 state)
      - hasBufferState(U32 state)
      - setBufferState(U32 state , bool flag)
        - onAddBufferState(U32 state)
        - onRemoveBufferState(U32 state)
      - removeBufferState(U32 state)
      - clearBufferStates()
    - Effect
      - BufferEffect
      - PlayEffect
    - callBack
      - onAddToGameScene
  - CreatureEntity
    - Follower
      - Baby
      - God
      - Pet
    - NPC
    - Player
      - MainPlayer
  - SceneItem
- DynamicShape
  - SceneItem
  - MagicDragon
  - MagicWeapon
- CharacterEditor
  - CreatureEntity
  - Mount
- BehaviorController
  - FollowBehavior
  - NormalBehavior
- EntityManager
  - 

 # 编程

## 类设计

 一个类拆分成好多个部分

- 外部访问接口
  - 对象和函数名
- 
