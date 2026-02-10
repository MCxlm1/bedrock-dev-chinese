---
title: 检测其他实体
category: Tutorials
tags:
    - intermediate
mentions:
    - ANightDazingZoroark
    - SmokeyStack
    - MedicalJewel105
    - SirLich
    - Lufurrius
    - TheItsNameless
    - QuazChick
description: 当附近有其他实体时触发事件。
---

您可能想过让您的实体在附近有其他实体时触发事件。本文详细介绍了各种已知的方法来实现这一功能。

## 实体传感器

这可能是检测其他实体的最基本方法。主要问题是它只接受一个条目，而且测试实体是否超出范围可能会非常困难。因为它是一个实体组件，您可以直接将其放入实体行为文件中并编辑Minecraft过滤器。下面是一个演示：

<CodeHeader>BP/entities/my_entity.json#components</CodeHeader>

```json
"minecraft:entity_sensor": {
    "sensor_range": 2.5, //这是检测半径（以方块为单位），用于检测附近的其他实体
    "relative_range": false, //如果为true，则传感器范围是在实体碰撞箱大小基础上的附加范围
    "require_all": true, //如果为true，则附近的所有实体都必须通过过滤条件才能发送事件
    "minimum_count": 1, //触发事件所需的最少实体数量。默认为1
    "maximum_count": 4, //触发事件所需的最多实体数量。默认为-1，表示无限
    "event_filters": { //您可以在此处放置任何过滤器，本示例中使用的过滤器仅检测玩家
        "test": "is_family",
        "subject": "other",
        "value": "player"
    },
    "event": "event:on_player_detected" //当event_filters中的所有条件都满足时触发的事件
}
```

## 执行命令

使用`/execute`命令，只要附近有其他实体，就可以执行命令。

您将要跟随的这个示例将使猪在检测到玩家时说"oink oink"，虽然您可以将其替换为您想要的任何内容。首先，复制粘贴这些BP动画。

<CodeHeader>BP/animations/detection_animation.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animations": {
        "animation.pig.find_player": {
            "animation_length": 0.05,
            "loop": true,
            "timeline": {
                "0": [
                    "/execute as @s if entity @e[type=player, r=4] run event entity @s wiki:player_detected"
                ]
            }
        },
        "animation.pig.find_no_player": {
            "animation_length": 0.05,
            "loop": true,
            "timeline": {
                "0": [
                    "/execute as @s unless entity @e[type=player, r=4] run event entity @s wiki:no_player_detected"
                ]
            }
        }
    }
}
```

第一个用于检测实体是否存在，另一个用于检测实体是否不存在。`/execute`命令中的`/event`部分使用的事件可用于添加[虚拟组件](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/dummy-components)或更新[角色属性](https://learn.microsoft.com/en-us/minecraft/creator/documents/introductiontoentityproperties)。

接下来，复制粘贴这个BP动画控制器。这假设您设置`/execute`命令的`/event`部分来添加或移除`minecraft:is_sheared`。

<CodeHeader>BP/animation_controllers/pig_animation_controllers.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animation_controllers": {
        "controller.animation.pig_find_player": {
            "initial_state": "default",
            "states": {
                "default": {
                    "animations": ["find_player"],
                    "transitions": [
                        {
                            "detected": "q.is_sheared"
                        }
                    ]
                },
                "detected": {
                    "animations": ["find_no_player"],
                    "transitions": [
                        {
                            "default": "!q.is_sheared"
                        }
                    ],
                    "on_entry": ["/say oink oink"]
                }
            }
        }
    }
}
```

最后，将此代码段复制粘贴到猪类实体的行为文件中。确保将其插入到`description`中。

<CodeHeader>BP/entities/my_entity.json#description</CodeHeader>

```json
"animations": {
    "manage_find_player": "controller.animation.pig_find_player",
    "find_player": "animation.pig.find_player",
    "find_no_player": "animation.pig.find_no_player"
},
"scripts": {
    "animate": [
        "manage_find_player"
    ]
}
```