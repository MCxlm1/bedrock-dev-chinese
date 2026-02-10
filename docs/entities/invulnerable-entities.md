---
title: 无敌实体
category: Tutorials
tags:
    - beginner
mentions:
    - SirLich
    - Joelant05
    - solvedDev
    - MedicalJewel105
description: 学习如何制作无敌实体。
---

## 使用伤害传感器

禁用实体伤害的最好和最灵活的方法是使用`minecraft:damage_sensor`组件。该组件允许我们使用`filters`来确定哪些伤害源可以伤害我们的实体。

学习此组件的最佳方法是使用伤害传感器的原版示例或阅读[文档](https://bedrock.dev/docs/stable/Entities#minecraft:damage_sensor)

### 完全无敌实体

<CodeHeader>BP/entities/entity.json#minecraft:entity/components</CodeHeader>

```json
"minecraft:damage_sensor": {
    "triggers": {
        "cause": "all",
        "deals_damage": "no"
    }
}
```

### 禁用来自玩家的伤害

<CodeHeader>BP/entities/entity.json#minecraft:entity/components</CodeHeader>

```json
"minecraft:damage_sensor": {
    "triggers": {
        "on_damage": {
            "filters": {
                "test": "is_family",
                "subject": "other",
                "value": "player"
            }
        },
        "deals_damage": "no"
    }
}
```

## 最小生命值

`minecraft:health`组件中的`min`属性允许我们制作不会死亡的无敌实体。这包括使用`/kill @e`时。这不被认为是好的解决方案，因为这样的实体很难去除。

如果您选择使用此组件，请确保您有另一种杀死实体的方法。从环境传感器、计时器或交互触发`minecraft:instant_despawn`是一个好解决方案。您也可以使用`/event`调用它，或考虑使用脚本API中的`Entity.remove()`{lang=js}。

<CodeHeader>BP/entities/entity.json#minecraft:entity/components</CodeHeader>

```json
"minecraft:health": {
    "value": 1,
    "max": 1,
    "min": 1
}
```

请注意，将其设置为0会破坏某些死亡和生成动画/效果。