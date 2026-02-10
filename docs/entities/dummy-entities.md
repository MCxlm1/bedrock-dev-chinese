---
title: 虚拟实体
category: Tutorials
tags:
    - beginner
mentions:
    - SirLich
    - Joelant05
    - MedicalJewel105
    - aexer0e
description: 虚拟实体是不可见的实体，在幕后用于游戏玩法目的。
---

虚拟实体是不可见的实体，在幕后用于游戏玩法目的。虚拟实体是一个非常有用的工具，本文档将涵盖它们的一些用途，以及展示如何设置资源方面的事情。

## 使用虚拟实体

这是虚拟实体如何使用的非详尽列表：

-   **用于数据存储**：通过向实体添加标签，我们可以将其用作"游戏管理器"，就像以前使用盔甲架一样。
-   **作为命名实体**：通过为虚拟实体命名标签，并使用`execute`来选择它，您可以使命令块以漂亮的显示名称`/say`。
-   **作为位置标记**：您可以在虚拟实体所在位置运行`execute`命令以获得该位置的相对坐标。
-   **作为路径点**：通过使实体对您的虚拟实体具有攻击性，您可以通过在某处放置虚拟实体来将实体寻路到任意位置。

## 创建虚拟实体

### 行为实体

您可以使用任何您喜欢的行为，但这里有一个很好的模板。重要的方面是：无伤害，且不能被推动。

<CodeHeader>BP/entities/dummy.json</CodeHeader>

```json
{
    "format_version": "1.21.50",
    "minecraft:entity": {
        "description": {
            "identifier": "wiki:dummy",
            "is_summonable": true,
            "is_spawnable": false,
            "is_experimental": false
        },
        "components": {
            "minecraft:cannot_be_attacked": {}, //可选，阻止实体攻击拥有者实体，除非它们具有"minecraft:ignore_cannot_be_attacked"组件。
            "minecraft:breathable": {
                //可选，允许实体在水下呼吸
                "breathes_water": true
            },
            "minecraft:physics": {
                "has_gravity": false, //可选，允许实体不受重力或水的影响
                "has_collision": false
            },
            "minecraft:custom_hit_test": {
                "hitboxes": [
                    {
                        "pivot": [0, 100, 0],
                        "width": 0,
                        "height": 0
                    }
                ]
            },
            "minecraft:damage_sensor": {
                "triggers": {
                    "deals_damage": false
                }
            },
            "minecraft:pushable": {
                "is_pushable": false,
                "is_pushable_by_piston": false
            },
            "minecraft:collision_box": {
                "width": 0.0001,
                "height": 0.0001
            }
        }
    }
}
```

如果您想完全禁用碰撞（以便可以在其位置放置方块），您可以使用箭头运行时标识符，但可能会有一些副作用。

### 资源实体

<CodeHeader>RP/entity/dummy.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "minecraft:client_entity": {
        "description": {
            "identifier": "wiki:dummy",
            "materials": {
                "default": "entity_alphatest"
            },
            "geometry": {
                "default": "geometry.dummy"
            },
            "render_controllers": ["controller.render.dummy"],
            "textures": {
                "default": "textures/entity/dummy"
            }
        }
    }
}
```

### 几何模型

<CodeHeader>RP/models/entity/dummy.json</CodeHeader>

```json
{
    "format_version": "1.12.0",
    "minecraft:geometry": [
        {
            "description": {
                "identifier": "geometry.dummy",
                "texture_width": 16,
                "texture_height": 16
            }
        }
    ]
}
```

### 渲染控制器（可选）

<CodeHeader>RP/render_controllers/dummy.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "render_controllers": {
        "controller.render.dummy": {
            "geometry": "Geometry.default",
            "textures": ["Texture.default"],
            "materials": [
                {
                    "*": "Material.default"
                }
            ]
        }
    }
}
```

### 纹理（可选）

您可以将纹理位置留空，或在Blockbench中打开模型并创建空白纹理。