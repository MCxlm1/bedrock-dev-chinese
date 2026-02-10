---
title: 实体攻击
category: Tutorials
mentions:
    - Lufurrius
    - TheDoctor15
    - SirLich
    - MedicalJewel105
    - epxzzy
    - ThomasOrs
tags:
    - intermediate
description: 学习如何正确地制作实体攻击。
---

实体攻击是一个复杂的主题，需要许多不同的组件才能正确工作：

-   导航和移动能力以移向目标
-   目标选择能力以选择要攻击的实体
-   攻击类型，如近战或远程
-   攻击伤害和效果

## 选择目标

### 移动

在生物能够攻击之前，它将需要各种[移动组件](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/entity-movement)。

在开始创建实体攻击之前，您应该确保您的实体能够四处走动并导航其周围环境。

:::warning
即使您正在制作一个不动的实体（如炮塔），您仍需要添加导航组件，以便您的实体能够找到要射击的目标。
:::

### 触发敌意

有许多方法可以触发敌意。最常见的类型`nearest_attackable_target`在这里展示。通常允许您定义此实体感兴趣的攻击实体：

<CodeHeader></CodeHeader>

```json
"minecraft:behavior.nearest_attackable_target": {
  "must_see": true, //如果为true，潜在目标必须在生物的视线内
  "reselect_targets": true, //允许生物选择新目标，如果有一个比当前目标更近
  "within_radius": 25.0, //潜在目标必须在的半径
  "must_see_forget_duration": 17.0, //如果"must_see" = true，在忘记目标之前的时间
  "entity_types": [
    {
      "filters": { //要攻击的实体
        "test": "is_family",
        "subject": "other",
        "value": "player"
      },
      "max_dist": 48.0
    }
  ]
}
```

对于更精细的控制，您还可以考虑使用以下组件之一：

| 组件                                                | 注释                                                         |
| -------------------------------------------------------- | ------------------------------------------------------------ |
| minecraft:behavior.nearest_attackable_target             | 目标符合给定要求的实体                |
| minecraft:behavior.nearest_prioritized_attackable_target | 允许为每个过滤器设置"priority": [整数] |
| minecraft:behavior.defend_trusted_target                 | 目标攻击在过滤器中指定的任何实体的实体  |

但还有一个 - `minecraft:lookat`

最后一个组件与另外三个略有不同，因为它用于检测和瞄准尝试眼神接触的实体。它的结构如下：

<CodeHeader>BP/entities/enderman.json</CodeHeader>

```json
"minecraft:lookat": {
  "search_radius": 64.0,
  "set_target": true, //如果为true则成为有效目标
  "look_cooldown": 5.0,
  "filters": {
    "all_of": [
      {
        "subject": "other",
        "test": "is_family",
        "value": "player"
      },
      {
        "test": "has_equipment",
        "domain": "head",
        "subject": "other",
        "operator": "not",
        "value": "carved_pumpkin"  //所有头部未佩戴南瓜雕刻的玩家
      }
    ]
  }
}
```

### 目标选择

:::tip
本节向您展示如何配置上面解释的"目标"组件。
:::

生物通过使用[过滤器](https://bedrock.dev/docs/stable/Entities#Filters)来查找目标，可以通过`test`、`subject`、`operator`和`value`来确定哪些实体是有效目标。

<CodeHeader></CodeHeader>

```json
"entity_types":[
    {
        "filters":{
            "any_of":[
                {
                    "test":"is_family",
                    "subject":"other",
                    "operator":"==",
                    "value":"snow_golem"
                },
                {
                    "test":"is_family",
                    "subject":"other",
                    "operator":"==",
                    "value":"iron_golem"
                }
                //等于snow_golem或iron_golem的任何实体
            ]
        },
        "max_dist":24
    },
    {
        "filters":{
            "all_of":[
                {
                    "test":"is_family",
                    "subject":"other",
                    "operator":"==",
                    "value":"player"
                },
                {
                    "test":"has_equipment",
                    "subject":"other",
                    "domain":"head",
                    "operator":"!=",
                    "value":"turtle_helmet"
                }
                //等于player并且头部未佩戴turtle_helmet的任何实体
            ]
        },
        "max_dist":24
    }
]
```

这只会攻击`snow_golem`、`iron_golem`和**不**佩戴`turtle_helmet`的`player`。

## 攻击类型

以下是可用的攻击：

| 组件                                            | 注释                                                     |
| ---------------------------------------------------- | -------------------------------------------------------- |
| [minecraft:behavior.melee_attack](#melee)            | 对单个目标造成伤害                          |
| [minecraft:behavior.ranged_attack](#ranged)          | 向目标发射抛射物                      |
| [minecraft:area_attack](#area)                       | 有效地对范围内任何实体进行近战攻击      |
| [minecraft:behavior.knockback_roar](#knockback-roar) | 类似于minecraft:area_attack，但更加灵活 |

### 近战

近战攻击是最常见的攻击类型，它们造成击退，并且准确性为100%。

<CodeHeader></CodeHeader>

```json
"wiki:melee_attack": {
  "minecraft:attack": {
    "damage": 3,
    "effect_name": "slowness",
    "effect_duration": 20
  },
  "minecraft:behavior.melee_attack": {
    "priority": 3,
    "melee_fov": 90.0, //演员将使用的允许FOV，以确定是否可以进行有效的近战攻击
    "speed_multiplier": 1,
    "track_target": false,
    "require_complete_path": true
  }
}
```

设置伤害，选择生物效果，并更改一些附加属性。

在组件中定义的整数伤害值可以简单地是一个常量，或者是包含2个数字的字符串，用于可能值的范围。

`"damage": 3`每次结果为3

`"damage": [ 2, 6 ]`结果为2到6之间的任何整数

生物效果和持续时间计时器都是可选的，但当使用时，可用效果如下：

| 效果名称     |
| --------------- |
| speed           |
| slowness        |
| haste           |
| mining_fatigue  |
| strength        |
| instant_health  |
| instant_damage  |
| jump_boost      |
| nausea          |
| regeneration    |
| resistance      |
| fire_resistance |
| water_breathing |
| invisibility    |
| blindness       |
| night_vision    |
| hunger          |
| weakness        |
| poison          |
| wither          |
| health_boost    |
| absorption      |
| saturation      |
| levitation      |
| fatal_poison    |
| slow_falling    |
| conduit_power   |
| bad_omen        |
| village_hero    |
| darkness        |

### 远程

在设定间隔向目标发射指定的[抛射物](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/projectiles)。

<CodeHeader></CodeHeader>

```json
"wiki:ranged_attack": {
  "minecraft:behavior.ranged_attack": {
    "priority": 2,
    "ranged_fov": 90.0, //演员将使用的允许FOV，以确定是否可以进行有效的远程攻击
    "attack_interval_min": 1.0,
    "attack_interval_max": 3.0,
    "attack_radius": 15.0
  },
  "minecraft:shooter": {
    "def": "wiki:projectile"
  }
}
```

原版抛射物列表：

| 原版抛射物              |
| -------------------------------- |
| minecraft:arrow                  |
| minecraft:dragon_fireball        |
| minecraft:egg                    |
| minecraft:ender_pearl            |
| minecraft:fireball               |
| minecraft:fishing_hook           |
| minecraft:lingering_potion       |
| minecraft:llama_spit             |
| minecraft:skulker_bullet         |
| minecraft:small_fireball         |
| minecraft:snowball               |
| minecraft:splash_potion          |
| minecraft:thrown_trident         |
| minecraft:wither_skull           |
| minecraft:wither_skull_dangerous |
| minecraft:xp_bottle              |

只有一样物品会影响实体的远程攻击。弩。如果装备了它，首先需要"充能"实体才能发射任何东西。无论在`minecraft:shooter`中指定的抛射物是什么，为弩充能的物品应该始终是`minecraft:arrow`。

<CodeHeader></CodeHeader>

```json
"minecraft:behavior.charge_held_item": {
  "priority": 2,
  "items": [
    "minecraft:arrow"
  ]
}
```

一旦实现了`minecraft:behavior.charge_held_item`，实体就能够执行`minecraft:behavior.ranged_attack`的过程，然后需要再次充能。

### 区域

这些攻击会伤害设定半径内的所有实体。它与远程和近战都不同，因为这个组件实际上不需要目标。不管实体行为如何，_所有_实体都会受到影响。它似乎类似于近战攻击，因为它以类似的方式造成击退，尽管以恒定速率造成伤害。

<CodeHeader></CodeHeader>

```json
"minecraft:area_attack" : {
  "damage_range": 1, //距离（以方块为单位）
  "damage_per_tick": 2,
  "cause": "contact",
  "entity_filter": {
     "any_of": [
      {
        "test": "is_family",
        "subject": "other",
        "value": "player"
      },
      {
        "test": "is_family",
        "subject": "other",
        "value": "monster"
      }
    ]
  }
}
```

[实体伤害来源](https://bedrock.dev/docs/stable/Addons#Entity%20Damage%20Source)。考虑到这些是很重要的，因为原版中的某些物品可以防御其中一些，比如盔甲附魔，您也可以使用`minecraft:damage_sensor`使生物免疫特定来源。

### 击退咆哮

这个与`minecraft:area_attack`有很多相似之处，但这个组件具有更大的灵活性。

<CodeHeader></CodeHeader>

```json
"wiki:roar_attack": {
  "minecraft:behavior.knockback_roar":{
    "priority":2,
    "duration":0.7,
    "attack_time":0.2,
    "knockback_damage":1,
    "knockback_horizontal_strength":1,
    "knockback_vertical_strength":1,
    "knockback_range":5,
    "knockback_filters":{
      "test":"is_family",
      "subject":"other",
      "operator":"==",
      "value":"player"
    },
    "damage_filters":{
      "test":"is_family",
      "subject":"other",
      "operator":"==",
      "value":"player"
    },
    "on_roar_end":{
      "event":"wiki:other_event"
    },
    "cooldown_time":10
  }
}
```

这更像是一个伤害冲击波。在用途上极其多功能。产生一个粒子效果，可以通过向资源包的particles文件夹添加修改版的`knockback_roar.json`来禁用。

## 攻击的更多信息

实体攻击不必像生物对X目标敌对，执行X攻击，造成X伤害那样简单。

### 难度依赖攻击

为每个难度表达要使用的组件和值。

<CodeHeader>BP/entities/bee.json</CodeHeader>

```json
"easy_attack": {
    "minecraft:attack": {
        "damage": 2
    }
},
"normal_attack": {
    "minecraft:attack": {
        "damage": 2,
        "effect_name": "poison",
        "effect_duration": 10
    }
},
"hard_attack": {
    "minecraft:attack": {
        "damage": 2,
        "effect_name": "poison",
        "effect_duration": 18
    }
}
```

### 切换模式

您可以使用事件使您的生物仅在特定情况下攻击，或在不同类型的攻击之间切换。这可以通过简单的[事件](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/entity-events)和组件组的使用来实现。两个主要的例子是`minecraft:environment_sensor`和`minecraft:target_nearby_sensor`。这两个在结构上相当相似，区别在于一个是用于感知环境，另一个是用于测试目标距离。

#### 攻击

组件组需要定义不同的攻击模式，例如：

<CodeHeader></CodeHeader>

```json
"wiki:ranged_components": {
  "minecraft:shooter": {
    "def": "wiki:projectile"
  },
  "minecraft:behavior.ranged_attack": {
    "priority": 3,
    "ranged_fov": 90.0,
    "attack_interval_min": 1.0,
    "attack_interval_max": 3.0,
    "attack_radius": 15.0
  }
}
```

<CodeHeader></CodeHeader>

```json
"wiki:melee_components": {
  "minecraft:attack": {
    "damage": 6
  },
  "minecraft:behavior.melee_attack": {
    "priority": 3
  }
}
```

这些是您的攻击模式示例，但它们不是您唯一可以使用的。`wiki:ranged_components`和`wiki:melee_components`是其中组件的通用名称，它们可以有任意名称，但重要的是嵌套在其中的内容。

#### 事件

这些组件组本身实际上不会做任何事情。需要另一个组件组和一些事件来添加/移除攻击模式。

<CodeHeader></CodeHeader>

```json
"wiki:melee_swap": {    //触发时，添加远程组件组并移除近战组件组
  "remove": {
    "component_groups": [
      "wiki:ranged_components"
    ]
  },
  "add": {
    "component_groups": [
      "wiki:melee_components"
    ]
  }
}
```

<CodeHeader></CodeHeader>

```json
"wiki:ranged_swap": {   //触发时，添加近战组件组并移除远程组件组
  "remove": {
    "component_groups": [
      "wiki:melee_components"
    ]
  },
  "add": {
    "component_groups": [
      "wiki:ranged_components"
    ]
  }
}
```

这些事件实际上是通过添加和移除不同的组件组来开启和关闭攻击模式。

#### 传感器

要触发事件，使用另一个组件组。传感器是可以在满足某些条件时触发事件的组件。以下是两个不同传感器的示例：

-   用于感知生物和目标之间的距离

<CodeHeader></CodeHeader>

```json
"wiki:switcher_range": {
  "minecraft:target_nearby_sensor": {
    "inside_range": 4.0,
    "outside_range": 5.0,
    "must_see":  true,
    "on_inside_range": { //当目标在4格范围内时，触发"wiki:melee_swap"事件
      "event": "wiki:melee_swap",
      "target": "self"
    },
    "on_outside_range": { //当目标在5格范围外时，触发"wiki:ranged_swap"事件
      "event": "wiki:ranged_swap",
      "target": "self"
    }
  }
}
```

-   用于感知生物暴露的环境的某些特征

<CodeHeader></CodeHeader>

```json
"wiki:switcher_environment": {
  "minecraft:environment_sensor": {
    "triggers": [
      {
        "filters": { //在水下时，触发"wiki:melee_swap"事件
          "test": "is_underwater",
          "subject": "self",
          "operator": "==",
          "value": true
        },
        "event": "wiki:melee_swap"
      },
      {
        "filters": { //不在水下时，触发"wiki:ranged_swap"事件
          "test": "is_underwater",
          "subject": "self",
          "operator": "==",
          "value": false
        },
        "event": "wiki:ranged_swap"
      }
    ]
  }
}
```

这使用`过滤器`，类似于[初始目标选择](#target-selecting)的方式。

:::tip
您不仅限于只有2种攻击类型，您可以有任意多的类型！只需确保有相应的事件和传感器来配合它们。
:::

## 视觉动画

攻击和动画紧密相关。在资源包中，需要以下3个目录：

-   animations (entityname.animation.json)
-   animation_controllers (entityname.animation_controller.json)
-   entity (entityname.json)

或者只要您知道原版动画和动画控制器的名称，就可以在后面的目录和文件夹中定义它们。

### 动画

动画是不言自明的。文件本身包含给定实体的所有特定动画。推荐的制作动画方法是使用[blockbench](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/guide/blockbench)。

虽然也可以在简单的文本编辑器中创建它们。

| 原版攻击动画                    |
| -------------------------------------------- |
| "animation.zombie.attack_bare_hand"          |
| "animation.skeleton.attack.v1.0"             |
| "animation.humanoid.bow_and_arrow.v1.0"      |
| "animation.humanoid.damage_nearby_mobs.v1.0" |

动画的一些示例。在/vanilla_resource_pack/animations中找到所有动画。

### 动画控制器

触发动画的状态列表。

| 原版攻击动画控制器           |
| ---------------------------------------------- |
| "controller.animation.zombie.attack_bare_hand" |
| "controller.animation.skeleton.attack"         |
| "controller.animation.humanoid.bow_and_arrow"  |
| "controller.animation.humanoid.attack"         |

动画控制器的一些示例。在/vanilla_resource_pack/animation_controllers中找到所有动画控制器。

关于动画的更多信息可以[在这里](https://bedrock.dev/docs/stable/Animations)找到。