---
title: 注视实体
category: Tutorials
tags:
    - intermediate
mentions:
    - shanewolf38
    - MedicalJewel105
    - TheItsNameless
    - SmokeyStack
description: 以下教程提供了一种资源包方法来检测玩家何时正在注视一个实体。
---

以下教程提供了一种资源包方法来检测玩家何时正在注视一个实体。下面的代码必须放置在将被玩家注视的实体内部，并将提供一个变量`v.look_at_entity`，当实体被注视时返回true。

## 变量

<CodeHeader>RP/entity/mob.entity.json</CodeHeader>

```json
"pre_animation": [
  "v.look_at_entity = Math.abs(Math.abs(q.rotation_to_camera(1) - q.camera_rotation(1)) - 180) < (20 / q.distance_from_camera) && Math.abs(q.rotation_to_camera(0) + q.camera_rotation(0)) < (10 / q.distance_from_camera);"
],
```

:::tip
由于查询`q.rotation_to_camera`基于实体的原点（它们的脚部），垂直检测范围将基于实体底部。下面的代码为垂直角度创建了一个修改后的变量，考虑了位置偏移，使垂直检测范围基于实体的中心。
:::

<CodeHeader>RP/entity/mob.entity.json</CodeHeader>

```json
"pre_animation": [
  "v.rotation_to_camera_0 = -Math.atan2(-q.distance_from_camera * Math.sin(q.rotation_to_camera(0)) - 1, q.distance_from_camera * Math.cos(q.rotation_to_camera(0)));",
  "v.look_at_entity = Math.abs(Math.abs(q.rotation_to_camera(1) - q.camera_rotation(1)) - 180) < (20 / q.distance_from_camera) && Math.abs(v.rotation_to_camera_0 + q.camera_rotation(0)) < (60 / q.distance_from_camera);"
],
```

## 修改

提供的代码对于标准Minecraft生物大小（宽1格，高2格）非常精确，但对于不同尺寸的实体应更改参数。`- 1`控制生物中心的位置偏移（-向上，+向下），`20`控制水平角度灵敏度，`60`控制垂直角度灵敏度。

## 解释

该变量通过检查实体看向玩家所需旋转角度是否与玩家看向实体所需旋转角度相反来检测玩家何时看向实体。水平和垂直角度灵敏度由实体到摄像机的距离调整以保持准确性。