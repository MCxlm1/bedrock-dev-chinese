---
title: 禁用团队伤害
description: 学习如何禁用同一团队中玩家之间的伤害。
category: Tutorials
tags:
    - intermediate
mentions:
    - SirLich
    - solvedDev
    - Joelant05
    - MedicalJewel105
    - Lufurrius
    - TCLynx
    - QuazChick
---

:::warning REALMS

这在Realms上将不起作用，原因是Realms中存在一个bug，即行为包中的修改后的[player.json](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/player.json)文件无法工作，游戏会忽略它们。

这在未来可能会被修复，但在1.20.15版本中仍未修复。这也适用于较老版本的Minecraft。

:::

## 来自玩家的伤害

如果您希望禁用团队伤害（这样一个人就不能伤害他们的队友），请为每个队友分配一个带有团队名称的标签（在本例中我将使用`team1`、`team2`、`team3`和`team4`）。

现在将此伤害传感器组件添加到您的[player.json](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/player.json)的`"components": {}`中。请参见注释以了解说明。

<CodeHeader>BP/entities/player.json > minecraft:entity > components</CodeHeader>

```json
"minecraft:damage_sensor": {
    "triggers": [
        {
            // 如果您已经有伤害传感器，只需将此对象复制到"triggers"数组中
            "on_damage": {
                "filters": {
                    "any_of": [
                        {
                            "all_of": [
                                { "test": "has_tag", "value": "team1" }, // 玩家是否有此标签？
                                { "test": "has_tag", "subject": "other", "value": "team1" } // 如果是，他们试图伤害的实体是否有此标签？
                            ]
                        },
                        {
                            "all_of": [
                                { "test": "has_tag", "value": "team2" }, // 每个团队重复此操作
                                { "test": "has_tag", "subject": "other", "value": "team2" }
                            ]
                        },
                        {
                            "all_of": [
                                { "test": "has_tag", "value": "team3" },
                                { "test": "has_tag", "subject": "other", "value": "team3" }
                            ]
                        },
                        {
                            "all_of": [
                                { "test": "has_tag", "value": "team4" },
                                { "test": "has_tag", "subject": "other", "value": "team4" }
                            ]
                        },
                        {
                            "all_of": [
                                { "test": "has_tag", "value": "team5" },
                                { "test": "has_tag", "subject": "other", "value": "team5" }
                            ]
                        }
                    ]
                }
            },
            "deals_damage": "no" // 如果这些过滤器中的任何一个在当前攻击交互中评估为真，目标将不会受到伤害。
        }
    ]
}
```

## 来自抛射物的伤害

由于抛射物实体使用的原始过滤器，您必须使用完全不同的方法来实现此目的。

这个过程使用：

-   标签
-   计时器
-   条件伤害
-   函数

<CodeHeader>BP/entities/player.json</CodeHeader>

```json
"components": {
    "minecraft:timer": { // 这是为了将团队标签应用到抛射物附近
        "time": [0, 0],  // 未标记的抛射物，通过一个事件。
        "looping": true,
        "time_down_event": {
            "event": "wiki:projectile_team",
            "target": "self"
        }
    },
    "minecraft:hurt_on_condition": { // 抛射物将无法直接造成
        "damage_conditions": [       // 伤害，所以我们会给
            {                        // 玩家添加标签，这将触发...
                "filters": {
                    "test": "has_tag",
                    "value": "damage"
                },
                "cause": "projectile",
                "damage_per_tick": 4
            }
        ]
    },
    "minecraft:damage_sensor": {     // ...这反过来会触发一个事件
        "triggers": {                // 以移除此标签，因此伤害只
            "cause": "projectile",   // 发生一次。
            "deals_damage": true,
            "on_damage": {
                "filters": {
                    "test": "has_tag",
                    "value": "damage"
                },
                "event": "wiki:stop_damage"
            }
        }
    }
},
"events": {
    "wiki:projectile_team": {  // 此处的函数将根据
        "queue_command": {     // 玩家拥有的团队标签应用标签。
            "command": ["function wiki-apply_team"]
        }
    },
    "wiki:stop_damage": {      // 简单移除伤害标签的事件。
        "queue_command": {
            "command": ["tag @s remove damage"]
        }
    }
}
```

<CodeHeader>BP/functions/wiki-apply_team.mcfunction</CodeHeader>

```
execute as @s[tag=team1] at @s run tag @e[rm=0,r=1,c=1,type=arrow,tag=] add team1
execute as @s[tag=team2] at @s run tag @e[rm=0,r=1,c=1,type=arrow,tag=] add team2
execute as @s[tag=team3] at @s run tag @e[rm=0,r=1,c=1,type=arrow,tag=] add team3
execute as @s[tag=team4] at @s run tag @e[rm=0,r=1,c=1,type=arrow,tag=] add team4
```

<CodeHeader>BP/entities/arrow.json</CodeHeader>

```json
"components": {
    "minecraft:projectile": {
        "on_hit": {           // 击中时，触发一个事件...
           "definition_event": {
                "affect_projectile": true,
                "event_trigger": {
                    "event": "wiki:hit",
                    "target": "self"
                }
           },
           "remove_on_hit": {}
        }
    }
},
"events": {
    "wiki:hit": {            // ...执行一个函数，给不同团队的玩家
       "queue_command": {    // 添加伤害标签！
            "command": ["function wiki-apply_damage"]
       }
    }
}
```

<CodeHeader>BP/functions/wiki-apply_damage.mcfunction</CodeHeader>

```
execute as @s[tag=team1] at @s run tag @p[rm=0,r=1,tag=!team1] add damage
execute as @s[tag=team2] at @s run tag @p[rm=0,r=1,tag=!team2] add damage
execute as @s[tag=team3] at @s run tag @p[rm=0,r=1,tag=!team3] add damage
execute as @s[tag=team4] at @s run tag @p[rm=0,r=1,tag=!team4] add damage
```

如果您修改[arrow.json](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/arrow.json)，请考虑组件组。