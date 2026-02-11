---
title: 生成物品
description: 了解如何生成物品实体。
tags:
    - intermediate
mentions:
    - SirLich
    - Joelant05
    - Dreamedc2015
    - yanasakana
    - MedicalJewel105
    - aexer0e
    - Xterionix
---

在世界上生成一个物品，就像掉落一样，是很常见的需求。本页将介绍通过各种方法实现这一点，包括实体死亡、交互和通用方法。

## /loot

到目前为止生成物品的最简单方法是使用/loot。格式如下：

```
/loot spawn ~ ~ ~ loot "entities/cow"
```

<CodeHeader>BP/loot_tables/entities/cow.json</CodeHeader>

```json
"minecraft:loot": {
    "table": "loot_tables/entities/cow.json"
}
```

## 实体死亡

另一种生成物品的简单方法——通常也是最常见的方法——是在实体死亡时掉落物品。这是通过向实体添加`minecraft:loot`组件并将其链接到包含您希望掉落物品的相应战利品表（在以下示例中为`forium`）来完成的。

<CodeHeader>BP/entities/my_entity.json#components</CodeHeader>

```json
"minecraft:loot": {
    "table": "loot_tables/entities/forium.json"
}
```

## 假实体死亡

我们可以在一个[假实体](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/dummy-entities)上使用`minecraft:loot`，当它生成时就会死亡，从而创建一个`drop_entity`。这个实体可以像`/summon wiki:drop_entity`一样召唤来生成物品。这对于死亡粒子或声音不是问题的情况很有用。

行为：

<CodeHeader>BP/entities/my_entity.json</CodeHeader>

```json
{
    "format_version": "1.16.0",
    "minecraft:entity": {
        "description": {
            "identifier": "wiki:drop_entity",
            "is_spawnable": true,
            "is_summonable": true,
            "is_experimental": false
        },

        "components": {
            // 导致实体生成时死亡
            "minecraft:health": {
                "value": 0
            },
            "minecraft:loot": {
                "table": "loot_tables/entities/some_loot.json"
            }
        }
    }
}
```

## 交互

这里有一个名为"box"的实体示例，它将在交互时掉落其内容。`spawn_items`中的表链接到包含想要掉落物品的战利品表。在这种特定情况下，当实体交互时还会调用`break_box`事件，添加一个移除箱子的组件组。

请注意，如果交互时不移除实体，它可以再次交互并会生成物品。如果交互后实体应保持存在，可以向实体添加`cooldown`参数以防止在指定时间内进行交互。或者，可以调用一个事件来移除包含此`minecraft:interact`组件的组件组。

<CodeHeader>BP/entities/my_entity.json#components</CodeHeader>

```json
"minecraft:interact": {
    "interactions": [
        {
            "on_interact": {
                "filters": {
                    "test": "is_family",
                    "subject": "other",
                    "value": "player"
                },
                "event": "break_box",
                "target": "self"
            },
            "swing": true,
            "spawn_items": {
                "table": "loot_tables/entities/box.json"
            }
        }
    ]
}
```

## 通用方法

这是一种几乎可用于任何场景的方法：实体死亡、基于动画的交互、一般物品掉落。特别是为在没有死亡动画、声音或粒子的情况下掉落物品而创建了这种方法。

设置物品掉落需要几个部分：一个具有行为的新实体、相应的动画控制器、隐形实体的资源（参考假实体教程）和一个战利品表。设置好后，将实体生成在要掉落物品的位置。如果需要多个物品，可以为每个物品设置带有生成事件的组件组。

### 行为

使用`minecraft:behavior.drop_item_for`组件配合`minecraft:navigation.walk`组件生成物品，后者是前者工作的必要条件。注意，尽管文档中如此定义，以下的`time_of_day_range`参数并未按如下方式初始化，这是正确功能所必需的。如果希望在玩家离得很远时也能掉落物品，则必须将`max_dist`参数增加到适当值。

此行为似乎会在物品掉落时将生物推后。因此，必须将实体稍微高于地面生成（或在下面的动画控制器中将其传送到上方）以避免物品在生成位置几格之外生成。减小碰撞箱的大小也可能有所帮助。

<CodeHeader>BP/entities/my_entity.json#components</CodeHeader>

```json
"minecraft:navigation.walk": {},
"minecraft:behavior.drop_item_for": {
    "priority": 1,
    "max_dist": 16,
    "loot_table": "loot_tables/entities/forium.json",
    "time_of_day_range": [0.0, 1.0]
}
```

### 动画控制器

**以下动画控制器必须链接到实体**以在召唤时移除它。或者，可以使用带时间轴的动画。如果您不确定如何执行此操作，请参考实体命令教程。

将实体传送到虚空中不会产生死亡动画、声音或粒子。使用两个过渡以确保它不会在生成的同一刻被杀死。

<CodeHeader>BP/animation_controllers/my_entity.ac.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animation_controllers": {
        "controller.animation.drop_items.die": {
            "initial_state": "spawn",
            "states": {
                "spawn": {
                    "transitions": [
                        {
                            "delay": "1"
                        }
                    ]
                },
                "delay": {
                    "transitions": [
                        {
                            "die": "1"
                        }
                    ]
                },
                "die": {
                    "on_entry": ["/tp @s ~ -200 ~"]
                }
            }
        }
    }
}
```

## 结构方法

还有一种有趣的生成物品的方法——通过结构。
您可以用`structure_void`（这样空气不会在结构加载时替换方块）填充一个结构，并将物品丢入其中。
此方法允许我们保持物品数据（例如耐久度）。
然后您可以在任何时间、任何地点加载此结构。

![](/assets/images/items/spawning-items/structure-method.png)