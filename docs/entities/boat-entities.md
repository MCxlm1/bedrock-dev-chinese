---
title: 创建船只
category: Tutorials
tags:
    - intermediate
mentions:
    - SirLich
    - Joelant05
    - MedicalJewel105
    - StealthyExpertX
    - TheItsNameless
description: 学习如何制作船只行为。
---

:::warning 需要格式版本1.16.100或更低

行为格式版本现在需要1.16.100或更低才能使`minecraft:behavior.rise_to_liquid_level`和`minecraft:buoyant`方法正常工作。
如果您发现了能在更新的格式版本中工作的新的方法，您应该考虑通过更新维基来贡献。
:::

## 使用运行时标识符

您可以[在此处](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/runtime-identifier)阅读更多关于运行时标识符的信息。使用运行时标识符，您可以实现船只的大部分硬编码行为。然而，您的船只不会随您旋转，并且它始终朝向北方。

## 使用组件

目前，创建船只实体的最佳方法是使用组件。1.16版本引入了我们可以利用的新组件：`minecraft:behavior.rise_to_liquid_level`和`minecraft:buoyant`。striders（炽足兽）在原版中使用第一个组件使其在岩浆上漂浮，但我们也可以将其重新用于水中。

## 第一种方法：minecraft:behavior.rise_to_liquid_level

<CodeHeader>BP/entities/bar</CodeHeader>

```json
{
    "minecraft:entity": {
        "format_version": "1.14.0",
        "description": {
            "identifier": "wiki:boat",
            "is_summonable": true,
            "is_spawnable": true,
            "is_experimental": false
        },
        "components": {
            //这是执行魔法的组件
            "minecraft:behavior.rise_to_liquid_level": {
                "priority": 0,
                //此属性可以调整船只在水面以上的高度
                "liquid_y_offset": 0.5,
                //正垂直位移，换句话说，船将向上移动多少
                "rise_delta": 0.05,
                //负垂直位移，换句话说，船将向下移动多少
                "sink_delta": 0.05
                //使用rise_delta和sink_delta来模拟波浪/弹跳效果
            },

            //设置船只在水中的速度
            "minecraft:underwater_movement": {
                "value": 5
            },
            //此组件很重要，没有它船会下沉
            "minecraft:navigation.walk": {
                "can_sink": false
            },
            "minecraft:rideable": {
                "seat_count": 1,
                "family_types": ["player"],
                "interact_text": "action.interact.enter_boat",
                "seats": {
                    "position": [0, 0, 0]
                }
            },
            //如果希望船只能够通过WASD控制，请添加此组件
            "minecraft:input_ground_controlled": {},
            "minecraft:health": {
                "value": 10,
                "max": 10
            },
            //设置船只在地面的速度（如果不想让船只在地上移动，请设为零）
            "minecraft:movement": {
                "value": 3
            },
            //这是为了防止玩家退出船只时船不停止
            "minecraft:movement.basic": {},
            "minecraft:collision_box": {
                "width": 1,
                "height": 1
            },
            "minecraft:physics": {}
        }
    }
}
```

## 第二种方法：minecraft:buoyant

<CodeHeader></CodeHeader>

```json
{
    "minecraft:entity": {
        "format_version": "1.14.0",
        "description": {
            "identifier": "wiki:boat",
            "is_summonable": true,
            "is_spawnable": true,
            "is_experimental": false
        },
        "components": {
            "minecraft:buoyant": {
                //确定是否应考虑重力（在瀑布中有用）
                "apply_gravity": true,
                //范围：0-1。这控制船只在水面以上的高度
                "base_buoyancy": 1.0,
                //"波浪"使实体上下弹跳。大波浪只是放大了这种效果。注意：设置simulate_waves为false不会完全消除效果。
                "simulate_waves": true,
                //多有可能出现"大"波浪冲击船只
                "big_wave_probability": 0.03,
                //大波浪的强度如何
                "big_wave_speed": 10.0,
                //如果删除此组件，实体会被拖拽下沉的力度
                "drag_down_on_buoyancy_removed": 0,
                //此实体可以漂浮的液体方块。只允许真正的液体：岩浆和水
                "liquid_blocks": ["water"]
            },

            //设置船只在水中的速度
            "minecraft:underwater_movement": {
                "value": 5
            },
            //此组件很重要，没有它船会下沉
            "minecraft:navigation.walk": {
                "can_sink": false
            },
            "minecraft:rideable": {
                "seat_count": 1,
                "family_types": ["player"],
                "interact_text": "action.interact.enter_boat",
                "seats": {
                    "position": [0, 0, 0]
                }
            },
            //如果希望船只能够通过WASD控制，请添加此组件
            "minecraft:input_ground_controlled": {},
            "minecraft:health": {
                "value": 10,
                "max": 10
            },
            //设置船只在地面的速度（如果不想让船只在地上移动，请设为零）
            "minecraft:movement": {
                "value": 3
            },
            //这是为了防止玩家退出船只时船不停止
            "minecraft:movement.basic": {},
            "minecraft:collision_box": {
                "width": 1,
                "height": 1
            },
            "minecraft:physics": {}
        }
    }
}
```

## 使用哪种方法？

两种方法都适合但各有优缺点。如果想禁用弹跳效果，请使用第一种方法。如果想要更多的控制，请使用第二种方法。我使用第二种方法用于静态物体，如浮标，而使用第一种方法用于可移动实体，如船只，模拟原版行为。

## 自定义受伤动画

您可能也对[自定义受伤动画](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/visuals/custom-hurt-animations)感兴趣。