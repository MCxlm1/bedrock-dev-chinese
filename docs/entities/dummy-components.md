---
title: 虚拟组件
category: Documentation
mentions:
    - SirLich
    - jigarbov
    - MedicalJewel105
    - StealthyExpertX
    - TheItsNameless
description: 虚拟组件是"无用"的组件，仅用于数据存储。
---

:::warning 弃用警告

'虚拟组件'是一个较早的概念，主要已被[角色属性](https://learn.microsoft.com/en-us/minecraft/creator/documents/introductiontoentityproperties)所取代。您应该尽可能考虑使用角色属性。
:::

虚拟组件是"无用"的组件，仅用于数据存储。虚拟组件本身**不会**做任何事情。它们需要与辅助机制配对才能发挥任何功能。虚拟组件很有用，因为它们允许我们在实体上存储信息，并使用这些信息来驱动图形/游戏机制。

好的例子是`variant`和`mark_variant`。这些组件可以设置为整数值。在原版资源包中，此整数用于为猫和马选择纹理。另一个好例子是`is_tamed`，它被马用来处理它是否可骑乘。

虚拟组件很好，因为它们允许我们保存关于实体的数据，然后使用Molang查询这些数据。

## 整数虚拟组件

整数虚拟组件设置为整数值，这允许您存储数字，如1、10或1423。这些整数可以使用查询读取。整数虚拟组件是最有用的。

## 位虚拟组件

位虚拟组件存储单个位信息。即`True`或`False`。例如`is_tamed`，它要么是`False`（未添加到实体），要么是`True`（添加到实体）。

## 虚拟组件

| 类型      | 查询                                                         | 组件                    | 注释                                                                                                                             |
| --------- | ------------------------------------------------------------- | ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **整数**   | q.variant                                                     | minecraft:variant            |                                                                                                                                   |
| **整数**   | q.mark_variant                                                | minecraft:mark_variant       |                                                                                                                                   |
| **整数**   | q.skin_id                                                     | minecraft:skin_id            |                                                                                                                                   |
| **整数\*** | 类似过滤器："test": "is_color"，颜色如下所示。 | minecraft:color              | 还在材质中设置颜色。                                                                                                     |
| **整数\*** | 没有已知过滤器，但可以与"has_component"一起使用       | minecraft:color2             | 还在材质中设置颜色。                                                                                                     |
| 位       | q.is_illager_captain                                          | minecraft:is_illager_captain |                                                                                                                                   |
| 位       | q.is_baby                                                     | minecraft:is_baby            | 禁用`minecraft:breedable`的使用                                                                                             |
| 位       | q.is_sheared                                                  | minecraft:is_sheared         |                                                                                                                                   |
| 位       | q.is_saddled                                                  | minecraft:is_saddled         |                                                                                                                                   |
| 位       | q.is_tamed                                                    | minecraft:is_tamed           |                                                                                                                                   |
| 位       | q.is_chested                                                  | minecraft:is_chested         | 死亡时掉落箱子                                                                                                          |
| 位       | q.is_powered                                                  | minecraft:is_charged         |                                                                                                                                   |
| 位       | q.is_stunned                                                  | minecraft:is_stunned         |                                                                                                                                   |
| 位       | q.can_climb                                                   | minecraft:can_climb          | 将允许实体攀爬梯子                                                                                              |
| 位       | q.can_fly                                                     | minecraft:can_fly            | 将实体标记为能够飞行，寻路器将不受下方必须有实心方块的路径限制。 |
| 位       | q.can_power_jump                                              | minecraft:can_power_jump     | 允许实体像原版马一样进行强力跳跃。                                                                   |
| 位       | q.is_ignited                                                  | minecraft:is_ignited         |                                                                                                                                   |
| 位       | q.out_of_control                                              | minecraft:out_of_control     | 新的，由代码用于硬编码的船移动/粒子内容，以及Molang q。可能安全使用                                     |
| 位       | q.has_any_family('monster')                                   | minecraft:type_family        | 可以使用家族类型并从诸如'monster'这样的家族返回true或false的位值。                                    |

### color和color2组件的颜色

-   black（黑色）
-   blue（蓝色）
-   brown（棕色）
-   cyan（青色）
-   gray（灰色）
-   green（绿色）
-   light_blue（淡蓝色）
-   light_green（淡绿色）
-   magenta（洋红色）
-   orange（橙色）
-   pink（粉色）
-   purple（紫色）
-   red（红色）
-   silver（银色）
-   white（白色）
-   yellow（黄色）