# FBX文件

## 网格体

### 枢轴点

用于坐标变换

网格体的枢轴点决定了执行任意变换（平移、旋转、缩放）时所围绕的点

### 网格三角剖分

用于渲染

- 仅使用三角形进行网格体建模。这是最佳方案，能对最终结果进行最大程度的控制。
- 在3D软件中三角剖分网格体。这是较好方案，在导出之前可以进行清理和修改。
- 让FBX导出器三角剖分网格体。这是普通方案，无法进行清理，但适用于简单网格体。
- 让导入器三角剖分网格体。这是普通方案，无法进行清理，但适用于简单网格体。

### LOD 细节

减少三角剖分数，用于节省开销

### 碰撞网格体

用于碰撞检测

适用于对象不需要发生碰撞的开放或凹陷区域网格体

## 网格体组合

### 插槽

用于连接不同的网格体

## 网格样式

### 材质

决定光照反射率

### 纹理

决定物体本身颜色

### 法线贴图

用于光照渲染

# PMX文件

[简书链接](https://www.jianshu.com/p/d051639b6aed)

## 模型

- 点
  - 坐标
- 面
  - 点组合成面，面组合成模型

## 外观

- 顶点添加UV数据
  - 用于显示图案
- 顶点添加法线数据
  - 用于计算光照阴影

## 运动

- 顶点太多不好控制，太过精确的运动不实用浪费
- 让顶点成组再控制组进行运动，组与组可添加关系
- 模拟生物骨骼，点跟随骨骼运动
- 骨骼
  - 旋转骨骼
  - 平移骨骼
  - IK骨骼
  - 不可见骨骼
- 表情
  - 顶点表情
    - 记录变化量
    - 可叠加
  - UV表情
  - 骨骼表情

## 物理

- 运动容易穿模，不自然
- 刚体
  - 跟随骨骼型
    - 给物体增加碰撞
  - 物理演算型
    - 计算物理上的各种基本作用力
- Joint 连接点

# VMD文件
[简书链接](https://www.jianshu.com/p/ae312fb53fc3)

[MMD](https://mikumikudance.fandom.com/wiki/VMD_file_format)

## 文件格式

| 类型数量     | 类型格式     | 字节大小   |
| ------------ | ------------ | ---------- |
| 版本名字     | 模型名字     | 30 + 10/20 |
| 关键帧数量 n | 关键帧解析 m | 4  + m * n |
| 骨骼         | 111          |            |
| 表情         | 23           |            |
| 相机         | 61           |            |
| 光线         | 28           |            |

## Python代码

```python
class Vmd:

    def __init__(self):
        pass

    @staticmethod
    def from_file(filename, model_name_encode="shift-JIS"):

        with open(filename, "rb") as f:
            array = f.read()

        vmd = Vmd()

        VersionInformation = array[:30].decode("ascii")
        if VersionInformation.startswith("Vocaloid Motion Data file"):
            vision = 1
        elif VersionInformation.startswith("Vocaloid Motion Data 0002"):
            vision = 2
        else:
            raise Exception("unknow vision")

        vmd.vision = vision

        vmd.model_name = array[30: 30+10*vision].split(bytes([0]))[0].decode(model_name_encode)
        vmd.bone_keyframe_number = int.from_bytes(array[30+10*vision: 30+10*vision+4], byteorder='little', signed=False)
        vmd.bone_keyframe_record = []
        vmd.morph_keyframe_record = []
        vmd.camera_keyframe_record = []
        vmd.light_keyframe_record = []

        current_index = 34+10 * vision
        import struct
        for i in range(vmd.bone_keyframe_number):
            vmd.bone_keyframe_record.append({
                "BoneName": array[current_index: current_index+15].split(bytes([0]))[0].decode(model_name_encode),
                "FrameTime": struct.unpack("<I", array[current_index+15: current_index+19])[0],
                "Position": {"x": struct.unpack("<f", array[current_index+19: current_index+23])[0],
                            "y": struct.unpack("<f", array[current_index+23: current_index+27])[0],
                            "z": struct.unpack("<f", array[current_index+27: current_index+31])[0]
                            },
                "Rotation":{"x": struct.unpack("<f", array[current_index+31: current_index+35])[0],
                            "y": struct.unpack("<f", array[current_index+35: current_index+39])[0],
                            "z": struct.unpack("<f", array[current_index+39: current_index+43])[0],
                            "w": struct.unpack("<f", array[current_index+43: current_index+47])[0]
                            },
                "Curve":{
                    "x":(array[current_index+47], array[current_index+51], array[current_index+55], array[current_index+59]),
                    "y":(array[current_index+63], array[current_index+67], array[current_index+71], array[current_index+75]),
                    "z":(array[current_index+79], array[current_index+83], array[current_index+87], array[current_index+91]),
                    "r":(array[current_index+95], array[current_index+99], array[current_index+103], array[current_index+107])
                }


            })
            current_index += 111

        # vmd['MorphKeyFrameNumber'] = int.from_bytes(array[current_index: current_index+4], byteorder="little", signed=False)
        vmd.morph_keyframe_number = int.from_bytes(array[current_index: current_index+4], byteorder="little", signed=False)
        current_index += 4

        for i in range(vmd.morph_keyframe_number):
            vmd.morph_keyframe_record.append({
                'MorphName': array[current_index: current_index+15].split(bytes([0]))[0].decode(model_name_encode),
                'FrameTime': struct.unpack("<I", array[current_index+15: current_index+19])[0],
                'Weight': struct.unpack("<f", array[current_index+19: current_index+23])[0]
            })
            current_index += 23

        vmd.camera_keyframe_number = int.from_bytes(array[current_index: current_index+4], byteorder="little", signed=False)
        current_index += 4

        for i in range(vmd.camera_keyframe_number):
            vmd.camera_keyframe_record.append({
                'FrameTime': struct.unpack("<I", array[current_index: current_index+4])[0],
                'Distance': struct.unpack("<f", array[current_index+4: current_index+8])[0],
                "Position": {"x": struct.unpack("<f", array[current_index+8: current_index+12])[0],
                            "y": struct.unpack("<f", array[current_index+12: current_index+16])[0],
                            "z": struct.unpack("<f", array[current_index+16: current_index+20])[0]
                            },
                "Rotation":{"x": struct.unpack("<f", array[current_index+20: current_index+24])[0],
                            "y": struct.unpack("<f", array[current_index+24: current_index+28])[0],
                            "z": struct.unpack("<f", array[current_index+28: current_index+32])[0]
                            },
                "Curve": tuple(b for b in array[current_index+32: current_index+36]),
                "ViewAngle": struct.unpack("<I", array[current_index+56: current_index+60])[0],
                "Orthographic": array[60]
            })
            current_index += 61

        vmd.light_keyframe_number = int.from_bytes(array[current_index: current_index+4], byteorder="little", signed=False)
        current_index += 4

        for i in range(vmd.light_keyframe_number):
            vmd.light_keyframe_record.append({
                'FrameTime': struct.unpack("<I", array[current_index: current_index+4])[0],
                'Color': {
                    'r': struct.unpack("<f", array[current_index+4: current_index+8])[0],
                    'g': struct.unpack("<f", array[current_index+8: current_index+12])[0],
                    'b': struct.unpack("<f", array[current_index+12: current_index+16])[0]
                },
                'Direction':{"x": struct.unpack("<f", array[current_index+16: current_index+20])[0],
                            "y": struct.unpack("<f", array[current_index+20: current_index+24])[0],
                            "z": struct.unpack("<f", array[current_index+24: current_index+28])[0]
                            }
            })
            current_index += 28

        vmd_dict = {}
        vmd_dict['Vision'] = vision
        vmd_dict['ModelName'] = vmd.model_name
        vmd_dict['BoneKeyFrameNumber'] = vmd.bone_keyframe_number
        vmd_dict['BoneKeyFrameRecord'] = vmd.bone_keyframe_record
        vmd_dict['MorphKeyFrameNumber'] = vmd.morph_keyframe_number
        vmd_dict['MorphKeyFrameRecord'] = vmd.morph_keyframe_record
        vmd_dict['CameraKeyFrameNumber'] = vmd.camera_keyframe_number
        vmd_dict['CameraKeyFrameRecord'] = vmd.camera_keyframe_record
        vmd_dict['LightKeyFrameNumber'] = vmd.light_keyframe_number
        vmd_dict['LightKeyFrameRecord'] = vmd.light_keyframe_record

        vmd.dict = vmd_dict

        return vmd
    def output_keyframe(self):
        out =" bone_keyframe_number = " + str(self.bone_keyframe_number)
        out =out + "\n morph_keyframe_number = " + str(self.morph_keyframe_number)
        out =out + "\n camera_keyframe_number = " + str(self.camera_keyframe_number)
        out =out + "\n light_keyframe_number = " + str(self.light_keyframe_number)
        print(out)
```



## 骨骼动画

### 文件格式

| 类型                       | 长度 |                            含义 |
| -------------------------- | :--: | ------------------------------: |
| byte                       |  15  |               骨骼名称 BoneName |
| uint32_t                   |  4   |            关键帧时间 FrameTime |
| float*3                    |  12  |   x,y,z空间坐标 Translation.xyz |
| float*4                    |  16  | 旋转四元数x,y,z,w Rotation.xyzw |
| uint8_t * 16 or uint32 * 4 |  16  |          补间曲线x的坐标 XCurve |
| uint8_t * 16 or uint32 * 4 |  16  |          补间曲线y的坐标 YCurve |
| uint8_t * 16 or uint32 * 4 |  16  |          补间曲线z的坐标 ZCurve |
| uint8_t * 16 or uint32 * 4 |  16  |       补间曲线旋转的坐标 RCurve |
| byte                       | 111  |                            合计 |

### 适用性

- 可运行于包含骨骼名的骨骼
- 骨骼运动不包含缩放，在不同尺度下运行会比较奇怪吗？
  - 那么如果缩放用于坐标是否可以解决问题？
  - 不行，没有统一规定的骨骼大小呀

## 表情动画

- blender 似乎也无法解析

## 相机动画

## 光线动画



为什么可以用于不同骨骼
不同骨骼会出现什么问题？如何解决？
为什么导入UE4多了mesh
https://www.aplaybox.com/details/model/MxWT1GhpcLKt