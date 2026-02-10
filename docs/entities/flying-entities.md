---
title: 骑乘飞行实体
description: 学习如何制作可由玩家控制的飞行实体。
category: Tutorials
tags:
    - intermediate
mentions:
    - SirLich
    - Joelant05
    - Dreamedc2015
    - MedicalJewel105
    - aexer0e
    - imsolucid
    - nebulacrab
    - Lufurrius
    - TheItsNameless
    - Halo333X
    - causal-guide
    - QuazChick
---

无论是制作飞机还是龙，为飞行实体添加可控性从未如此简单！

## 空中控制输入

这是允许控制飞行实体的推荐方法，也是原版快乐恶魂所使用的。

通过使用设备上的移动输入（例如Windows上的WASD），`minecraft:input_air_controlled`组件会导致实体在玩家面对的方向上在3D空间中移动。
此组件要求实体具有`minecraft:movement.hover`组件或实体没有重力。

这目前还包括跳跃上升。

<CodeHeader></CodeHeader>

```json
"minecraft:input_air_controlled": {
  "strafe_speed_modifier": 1,
  "backwards_movement_modifier": 0.5
}
```

### 未被骑乘时应用重力

仅在实体未被骑乘时应用重力，允许实体在被骑乘时飞行，其余时间停留在地面。

这可以通过使用组件组实现！

#### 组件组

<CodeHeader>minecraft:entity > component_groups</CodeHeader>

```json
// 启用重力
"wiki:walking": {
    "minecraft:physics": {
        "has_gravity": true
    }
},
// 禁用重力
"wiki:flying": {
    "minecraft:physics": {
        "has_gravity": false
    },
    "minecraft:flying_speed": {
        "value": 0.08
    }
}
```

#### 组件

<CodeHeader>minecraft:entity > components</CodeHeader>

```json
// 允许实体被控制
"minecraft:input_air_controlled": {
    "strafe_speed_modifier": 1,
    "backwards_movement_modifier": 0.5
},
// 允许实体被玩家骑乘
"minecraft:rideable": {
    "seat_count": 1,
    "interact_text": "action.interact.ride.horse",
    "family_types": ["player"],
    "seats": {
        "position": [0.0, 0.63, 0.0]
    },
    "on_rider_enter_event": "wiki:start_flying",
    "on_rider_exit_event": "wiki:stop_flying"
},
// 禁用摔落伤害
"minecraft:damage_sensor": {
    "triggers": {
        "cause": "fall",
        "deals_damage": "no"
    }
}
```

#### 事件

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"wiki:start_flying": {
    "add": {
        "component_groups": ["wiki:flying"]
    },
    "remove": {
        "component_groups": ["wiki:walking"]
    }
},
"wiki:stop_flying": {
    "remove": {
        "component_groups": ["wiki:flying"]
    },
    "add": {
        "component_groups": ["wiki:walking"]
    }
}
```

## 大跳跃，慢下降

虽然不完全是"飞行"，但将实体的跳跃力量设置得很高，并在它下落时给予缓慢下落和速度效果，可能是最直接的方法。

要实现这一点，我们需要向实体添加`"minecraft:horse.jump_strength"`组件。添加此组件将允许您控制其跳跃力量，并在玩家按下跳跃按钮时禁用下马。

<CodeHeader></CodeHeader>

```json
"minecraft:horse.jump_strength": {
    "value": 7
}
```

我们也可以使用`"value"`作为一个对象来利用玩家按住跳跃按钮时看到的**范围条**。

<CodeHeader></CodeHeader>

```json
"minecraft:horse.jump_strength": {
    "value": { "range_min": 0.6, "range_max": 1.2 }
}
```

现在我们将在下落时给予它缓慢下落和速度效果，这样它就不会立刻下落。为此，我们将制作一个动画控制器，并在它不在地面上时给予这些效果：

（您可以阅读关于如何使用动画控制器执行命令的教程[这里](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/animation-controllers/entity-commands)。）

<CodeHeader></CodeHeader>

```json
"controller.animation.dragon.flying": {
    "states": {
        "default": {
            "transitions": [
                {
                    "jumping": "!q.is_on_ground"
                }
            ]
        },
        "jumping": {
            "transitions": [
                {
                    "default": "q.is_on_ground"
                }
            ],
            "on_entry": [
                "/effect @s slow_falling 20000 0 true",
                "/effect @s speed 20000 10 true"
            ],
            "on_exit": [
                "/effect @s clear"
            ]
        }
    }
}
```

我们还需要将它连接到我们的实体，如下所示：

<CodeHeader></CodeHeader>

```json
"description": {
    "identifier": "wiki:dragon",
    "is_spawnable": true,
    "is_summonable": true,
    "scripts": {
        "animate": [
            "flying"
        ]
    },
    "animations": {
        "flying": "controller.animation.dragon.flying"
    }
}
```

现在，我们应该有一个至少类似于飞行的机制。您可以更改跳跃力量和速度等值，但实体将始终使用此方法下落。