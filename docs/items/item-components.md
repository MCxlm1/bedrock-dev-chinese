---
title: 物品组件
description: 物品组件用于更改物品在世界中的外观和功能。
nav_order: 2
mentions:
    - SmokeyStack
    - QuazChick
---

:::tip 格式版本 1.21.130
在创建自定义物品时使用最新格式版本可以访问新功能和改进。本维基旨在分享有关自定义物品的最新信息，当前针对格式版本1.21.130。
:::

## 应用组件

物品组件用于更改物品在世界中的外观和功能。它们应用于[minecraft:item](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/documentation/Entities#minecraft_item)的`components`子项中。

<CodeHeader>BP/items/custom_item.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:custom_item",
            "menu_category": {
                "category": "items"
            }
        },
        "components": {
            "minecraft:icon": "wiki:custom_item"
        }
    }
}
```

## 组件列表

### 允许副手

确定物品是否可以放置在库存的副手槽中。

类型：布尔值

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:allow_off_hand": true
```

### 方块放置器

方块放置器物品组件。具有此组件的物品在使用时将放置一个方块。在格式版本1.20.10中从实验中发布。

在生存模式下使用时，物品将被消耗。

类型：对象

-   `block`: 字符串/对象
    -   定义将放置的方块。
-   `replace_block_item`: 布尔值
    -   详细了解替换方块物品[请查看](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/blocks/blocks-as-items#replacing-block-items)。
-   `use_on`: 数组
    -   包含此物品可使用方块的方块描述符列表。如果留空，将允许所有方块。有关使用行为的更多信息，请参见自定义物品使用优先级。
    -   这也适用于创造模式。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:block_placer": {
    "block": "wiki:custom_block",
    "use_on": [
        "minecraft:dirt",
        "wiki:custom_dirt"
    ]
}
```

### 捆绑包交互

在物品上启用捆绑包界面和功能。
该物品必须具有`minecraft:storage_item`组件才能使此组件生效。

_从格式版本1.21.40及更高版本的`Bundles`实验中发布。_

类型：对象

-   `num_viewable_slots`: 整数 (1-64)
    -   定义从捆绑包顶部可访问的物品堆叠的最大数量。
    -   插槽按行访问，从工具提示的底部从右到左填充。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:bundle_interaction": {
    "num_viewable_slots": 12
}
```

### 创造模式可破坏

确定物品在挥动时是否会破坏创造模式中的方块。

类型：布尔值

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:can_destroy_in_creative": true
```

### 可堆肥

允许此物品在堆肥箱中使用。

类型：对象

-   `composting_chance`: 浮点数 (0-100)
    -   堆肥等级增加的可能性（百分比）。

_从格式版本1.21.60及更高版本的`Upcoming Creator Features`实验中发布。_

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:compostable": {
    "composting_chance": 50 // 50% 机会增加堆肥等级
}
```

### 冷却

确定物品的冷却何时触发以及每次冷却应持续多长时间。

_需要格式版本[1.20.10](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/item-format-history#_1-20-10)或更高版本。_

#### 对象定义 {#cooldown-object}

-   `category`: 字符串
    -   此物品的冷却类别。
    -   相同类别的物品将共享冷却。
-   `duration`: 浮点数
    -   匹配类别的物品在再次可用之前需要冷却的时间长度（以秒为单位）。
    -   如果此值为负数，则会使物品无法使用。
-   `type`: 字符串（可选）
    -   确定冷却影响以下哪种类型的输入：
        -   `"use"`{lang=json}（默认）导致冷却在物品使用时开始，并在冷却激活期间防止使用物品。
        -   `"attack"`{lang=json}导致冷却在玩家手持物品攻击时开始，并在冷却激活期间防止使用物品进行攻击。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:cooldown": {
    "category": "wiki:cooldown",
    "duration": 0.2,
    "type": "use"
}
```

### 伤害

确定物品在攻击时会造成多少额外伤害。确定物品在攻击时会造成多少额外伤害。请注意，这必须是正值。

实体将受到的实际伤害是`value + 1`，根据文档中"额外伤害"的描述，这是因为手/物品具有默认值1伤害。
伤害值为`value % 256`。
使用有符号16位整数。2的补码创建负数范围。
`[32768-65536]` - 被视为负数。给物品的值将是`(-32768-0)`。所以负数范围是`[256*(256x+128) - 256*(256(x+1)))`，其中`x`是任意数字。

https://bugs.mojang.com/browse/MCPE-180073

类型：整数

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:damage": 10
```

### 伤害吸收

使物品能够吸收原本会对佩戴者造成的伤害。为此，物品需要具有耐久组件并装备在护甲槽中。

类型：对象

-   `absorbable_causes`: 数组
    -   可被物品吸收的伤害原因列表（如`entity_attack`和`magma`）。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:damage_absorption": {
    "absorbable_causes": ["all"]
}
```

### 挖掘器

确定物品挖掘特定方块的速度。

类型：对象

-   `destroy_speeds`: 对象 - 要挖掘的方块列表，带有相关的挖掘速度。
    -   `block`: 字符串/对象 - 物品将破坏的方块。
        -   `tags`: 字符串
            -   Molang查询
    -   `speed`: 整数
        -   方块被破坏的速度。
        -   可以为负。如果是负数，物品将无法破坏方块。
-   `use_efficiency`: 布尔值
    -   确定是否应受应用于此物品的`efficiency`附魔影响。
    -   似乎不起作用。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:digger": {
    "use_efficiency": true,
    "destroy_speeds": [
        {
            "block": {
                "tags": "q.any_tag('stone', 'metal')" // 注意并非所有方块都有标签；可能需要列出许多方块
            },
            "speed": 6
        }
    ]
}
```

### 显示名称

定义在显示物品名称时显示的文本，例如悬停文本。在格式版本1.20.0中从实验中发布。

不支持换行转义字符

类型：字符串

#### 示例

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:display_name": {
    "value": "secret_weapon"
}
```

#### 使用本地化键的示例

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:display_name": {
    "value": "item.snowball.name"
}
```

### 耐久

确定物品在损坏前可以承受多少伤害，并允许物品在合成中组合。在格式版本1.20.0中从实验中发布。

耐久不会在挖掘方块时隐式损伤自身。必须通过ScriptAPI处理。但是，当伤害生物时确实会隐式损伤自身。
每次对生物的攻击会使耐久减少2。这与武器的原版属性不匹配，但与工具的原版属性匹配。

与`minercraft:wearable`一起使用时，用物品攻击生物不会使耐久减少2。相反，当装备并被实体击中时，它会隐式减少1耐久。这与原版属性匹配。

https://bugs.mojang.com/browse/MCPE-180112

类型：对象

-   `damage_chance`: 对象 - 伤害概率是此物品失去耐久的百分比机会。默认设置为100。定义为具有最小值和最大值的整数范围。
    -   `min`: 整数
        -   耐久受损的最小机会。范围：[0, 100]。
    -   `max`: 整数
        -   耐久受损的最大机会。范围：[0, 100]。
-   `max_durability`: 整数
    -   最大耐久是此物品在损坏前可以承受的伤害量。这是必需参数，最小值为0。
    -   使用有符号16位整数。2的补码创建负数范围。`[32768-65536]` - 被视为负数。给物品的值将是`(-32768-0)`。所以负数范围是`[256*(256x+128) - 256*(256(x+1)))`，其中x是任意数字。
    -   https://bugs.mojang.com/browse/MCPE-180112

#### 伤害概率

用于计算不破坏机会。

-   无耐久 - 无论范围如何都是100%的时间
-   耐久I - 范围的50%
-   耐久II - 范围的33%
-   耐久III - 范围的25%

最大值不能大于最小值

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:durability": {
    "damage_chance": {
        "min": 0,
        "max": 100
    },
    "max_durability": 100
}
```

### 耐久传感器

启用物品在受到伤害时发出效果。

类型：对象

-   `durability_thresholds`: 数组
    -   项目定义耐久阈值以及达到每个阈值时发出的效果。
    -   当达到多个阈值时，仅考虑应用伤害后耐久最低的阈值。

#### 耐久阈值

类型：对象

-   `durability`: 整数
    -   当物品耐久值小于或等于此值时发出效果。
-   `particle_type`: 字符串
    -   达到阈值时发出的粒子效果。
-   `sound_event`: 字符串
    -   达到阈值时发出的声音效果。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:durability_sensor": {
    "durability_thresholds": [
        {
            "durability": 100,
            "particle_type": "minecraft:explosion_manual",
            "sound_event": "blast"
        },
        {
            "durability": 5,
            "sound_event": "raid.horn"
        }
    ]
}
```

### 可染色

允许物品通过炼药锅水染色。一旦染色，物品将显示在`minecraft:icon`组件中定义的`dyed`纹理，而不是`default`。

类型：对象

-   `default_color`: 字符串
    -   玩家染色物品前默认使用的可选颜色。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:dyeable": {
    "default_color": "#ffffff"
}
```

### 可附魔

确定可以将哪些附魔应用于物品。并非所有附魔都会对所有物品组件产生效果。

https://bugs.mojang.com/browse/MCPE-180331

类型：对象

-   `slot`: 字符串
    -   可以应用哪些附魔（例如，使用`bow`会使此物品像弓一样被附魔）。
    -   必填字段。
-   `value`: 整数
    -   附魔的值（最小为0）。
    -   必填字段
    -   值为`value % 256`

#### 可附魔槽位

| 槽位名称 |
| --------------- |
| `armor_feet` |
| `armor_torso` |
| `armor_head` |
| `armor_legs` |
| `axe` |
| `bow` |
| `cosmetic_head` |
| `crossbow` |
| `elytra` |
| `fishing_rod` |
| `flintsteel` |
| `hoe` |
| `pickaxe` |
| `shears` |
| `shield` |
| `shovel` |
| `sword` |
| `all` |

#### 附魔能力值

确定物品的附魔能力，影响潜在附魔的质量和数量。较高的值会提高保证更强大附魔的机会。下表详细介绍了不同材料的附魔能力，展示了它们获得附魔的能力。

| 材料 | 护甲附魔能力 | 剑/工具附魔能力 |
| --------- | -------------------- | ------------------------- |
| 木头 | N/A | 15 |
| 皮革 | 15 | N/A |
| 石头 | N/A | 5 |
| 链条 | 12 | N/A |
| 铁 | 9 | 14 |
| 金 | 25 | 22 |
| 钻石 | 10 | 10 |
| 海龟 | 9 | N/A |
| 下界合金 | 15 | 15 |
| 其他 | 1 | 1 |

有关附魔能力和其对游戏影响的深入探讨，请参阅[非官方Minecraft维基上的附魔机制](https://minecraft.wiki/w/Enchanting_mechanics#Enchantability)。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:enchantable": {
    "slot": "sword",
    "value": 10
}
```

### 实体放置器

允许物品将指定实体放置到世界中。在格式版本1.20.0中从实验中发布。

类型：对象

-   `dispense_on`: 数组
    -   包含此物品可分配在的方块描述符列表。如果留空，将允许所有方块。
    -   分配器的口必须指向空气方块或此数组中定义的方块。如果是空气方块，游戏会检查下方的方块是否与此数组中定义的方块匹配
-   `entity`: 字符串
    -   要放置在世界中的实体。
-   `use_on`: 数组
    -   包含此物品可使用方块的方块描述符列表。如果留空，将允许所有方块。有关使用行为的更多信息，请参见自定义物品使用优先级。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
    "minecraft:entity_placer": {
        "entity": "minecraft:spider",
        "dispense_on": [
            "minecraft:dirt"
        ],
        "use_on": [
            "minecraft:dirt"
        ]
    }
```

### 耐火

确定物品在掉落时是否应该能够承受火焰和熔岩而不是被摧毁。

#### 布尔定义 {#fire-resistant-boolean}

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:fire_resistant": {
    "value": true
}
```

### 食物

当物品具有食物组件时，它可以被玩家食用。
必须具有[`minecraft:use_modifiers`](#use-modifiers)组件才能正常工作。

要显示进食/饮用动画，还需要将[`minecraft:use_animation`](#use-animation)组件应用到物品。

类型：对象

-   `can_always_eat`: 布尔值
    -   如果为`true`，您总是可以吃这个物品（即使不饿）。
-   `nutrition`: 整数
    -   使用物品时添加到实体营养值的值。
    -   可以为负。
    -   最大值为32位整数限制
-   `saturation_modifier`: 浮点数
    -   饱和度修正值用于此公式：`(nutrition * saturation_modifier * 2)`，在应用饱和度增益时。
    -   值必须大于0
-   `using_converts_to`: 字符串
    -   使用时，转换为该字段中字符串指定的物品。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:food": {
    "can_always_eat": false,
    "nutrition": 3,
    "saturation_modifier": 0.6,
    "using_converts_to": "bowl"
}
```

### 燃料

允许物品用作熔炉中的燃料来'烹饪'其他物品。在格式版本1.20.0中从实验中发布。

最大值为`107374180`（包含）。此数字的原因是因为转换为刻度时，它达到了32位整数限制

类型：对象

-   `duration`: 浮点数
    -   此燃料将烹饪物品多长时间（以秒为单位）。最小值：0.05。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:fuel": {
    "duration": 3.0
}
```

### 闪亮效果

确定物品是否具有附魔闪亮渲染效果。

类型：布尔值

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:glint": false
```

### 手持装备

确定物品在手中时是否像工具一样渲染。

类型：布尔值

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:hand_equipped": true
```

### 悬停文字颜色

确定悬停在物品上时物品名称的颜色。

有效颜色可在此处找到：https://minecraft.wiki/w/Formatting_codes#Color_codes

类型：字符串

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:hover_text_color": "minecoin_gold"
```

### 图标

确定在UI和其他地方代表物品的图标。在格式版本1.20.10中从实验中发布。

#### 字符串定义 {#icon-string}

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:icon": "wiki:custom_item"
```

#### 对象定义 {#icon-object}

-   `textures`: 对象 - 此映射包含可用于物品图标的不同的纹理。护甲镶嵌纹理和调色板也可以在此处指定。图标纹理是与纹理文件关联的`resource_pack/textures/item_texture.json -> texture_data`对象的键。
    -   `default`
        -   用于物品的实际图标
    -   `dyed`
        -   [可染色](#dyeable)物品在炼药锅中染色后显示的图标。
    -   `icon_trim`
        -   当物品上有镶嵌时的图标叠加。
        -   `icon_trim`隐式回退到`minecraft:wearable`组件中的槽位类型。目前，只有当简称与物品标识符匹配时，图标才会叠加。这是否是错误还是功能尚不清楚。
    -   `bundle_open_back`
        -   在[bundle interaction](#bundle-interaction)工具提示中所选物品预览后显示的纹理。
    -   `bundle_open_front`
        -   在[bundle interaction](#bundle-interaction)工具提示中所选物品预览前显示的纹理。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:icon": {
    "textures": {
        "default": "wiki:custom_item"
    }
}
```

### 交互按钮

此组件是一个布尔值或字符串，确定触摸控制中是否显示交互按钮以及按钮上显示的文本。设置为`true`时，将使用默认的"Use Item"文本。

类型：布尔值/字符串

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:interact_button": "使用这个自定义物品"
```

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:interact_button": true
```

### 动能武器

导致物品在使用时伤害（以及卸下并击退）玩家移动方向上的每个实体（或向玩家移动的实体）。

#### 对象定义 {#kinetic-weapon-object}

-   `delay`: 整数
    -   确定动能伤害和效果开始应用之前的持续时间（以刻度为单位）。
-   `hitbox_margin`: 浮点数
    -   确定每个目标的碰撞箱需要离玩家视线方向多近才能受到动能攻击影响。
    -   默认情况下，没有额外的命中框边缘。
-   `reach`: 浮点数 [范围](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/documentation/shared-constructs#range-objects)
    -   确定范围（以方块为单位），实体必须与玩家相距多远才能受到动能攻击影响。
    -   默认情况下，距离玩家0到3个方块的实体将受到动能攻击影响。
-   `creative_reach`: 浮点数 [范围](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/documentation/shared-constructs#range-objects)
    -   确定玩家在创造模式下应用的`reach`。
    -   默认情况下，创造模式中的玩家将受限于正常的`reach`范围。
-   `damage_multiplier`: 浮点数
    -   确定动能攻击的基础伤害乘以什么值来获得乘积伤害值。
-   `damage_modifier`: 浮点数
    -   添加到乘积伤害值中以获得施加于每个目标的最终伤害值。
-   `damage_conditions`: 对象
    -   列出造成伤害需要满足的条件。
-   `dismount_conditions`: 对象
    -   列出每个目标被动能攻击从其骑乘的实体上卸下的条件。
    -   默认情况下，实体永远不会被卸下。
-   `knockback_conditions`: 对象
    -   列出每个目标受到动能攻击击退需要满足的条件。
    -   默认情况下，永远不会应用击退。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:kinetic_weapon": {
    "delay": 15,
    "reach": {
        "min": 2.0,
        "max": 4.5
    },
    "creative_reach": {
        "min": 2.0,
        "max": 7.5
    },
    "hitbox_margin": 0.25,
    "damage_multiplier": 0.7,
    "damage_conditions": {
        "max_duration": 300,
        "min_relative_speed": 4.6
    },
    "knockback_conditions": {
        "max_duration": 120,
        "min_speed": 5.1
    },
    "dismount_conditions": {
        "max_duration": 100,
        "min_speed": 14.0
    }
}
```

### 液体剪切

确定物品在使用时是否与液体方块交互。

与液体交互时，轮廓选择不能高亮显示液体下方的方块。交互发生在液体方块内部，而不是其侧面。

类型：布尔值

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:liquid_clipped": true
```

### 最大堆叠大小

确定物品可以堆叠多少个。

类型：整数

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:max_stack_size": 64
```

### 穿透武器

导致物品在攻击时伤害玩家视线方向直线上的所有实体。

-   如果玩家与其他实体之间有方块碰撞，伤害将被阻挡。

-   防止物品用于挖掘方块。

#### 对象定义 {#piercing-weapon-object}

-   `hitbox_margin`: 浮点数
    -   确定每个实体的碰撞箱需要离玩家视线方向多近才能受到伤害。
    -   默认情况下，没有额外的命中框边缘。
-   `reach`: 浮点数 [范围](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/documentation/shared-constructs#range-objects)
    -   确定范围（以方块为单位），实体必须与玩家相距多远才能受到伤害。
    -   默认情况下，距离玩家0到3个方块的实体将受到伤害。
-   `creative_reach`: 浮点数 [范围](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/documentation/shared-constructs#range-objects)
    -   确定玩家在创造模式下应用的`reach`。
    -   默认情况下，创造模式中的玩家将受限于正常的`reach`范围。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:piercing_weapon": {
    "reach": {
        "min": 2.0,
        "max": 4.5
    },
    "creative_reach": {
        "min": 2.0,
        "max": 7.5
    },
    "hitbox_margin": 0.25
}
```

### 弹射物

弹射物物品组件。弹射物物品像箭一样射出。在格式版本1.20.10中从实验中发布。

类型：对象

-   `minimum_critical_power`: 浮点数
    -   定义弹射物需要充能多长时间才能暴击。
-   `projectile_entity`: 字符串
    -   作为弹射物发射的实体。如果没有指定命名空间，则假定为`minecraft`。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:projectile": {
    "minimum_critical_power": 1.25,
    "projectile_entity": "arrow"
}
```

### 稀有度

通过改变名称文本的颜色来表示物品获取的难度。
如果也应用了`minecraft:hover_text_color`，则此组件将被覆盖且无效。

物品的稀有度值将在附魔时升级为`rare`，如果其基础稀有度已经是`rare`，则升级为`epic`。
`epic`稀有度的物品在附魔时保持不变。

类型：字符串

-   `common` 结果为白色名称。
-   `uncommon` 结果为黄色名称。
-   `rare` 结果为青色名称。
-   `epic` 结果为淡紫色名称。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:rarity": "rare"
```

### 唱片

唱片物品组件允许物品在音乐盒中使用时播放声音。

类型：对象

-   `comparator_signal`: 整数
    -   比较器方块使用的信号强度
    -   虽然此值可以是任何数字（甚至是负数！），但比较器信号仍然限制在[0, 15]之间。
-   `duration`: 浮点数
    -   声音事件持续时间，以秒为单位的浮点值。
-   `sound_event`: 字符串
    -   声音事件类型
    -   如果声音类型是任何原版音乐唱片，物品将具有艺术家名称的描述。当在音乐盒中播放时，玩家屏幕上会出现一个动作栏，描述正在播放的唱片。
    -   无论使用什么声音类型，物品的文字颜色将变为青色，就像原版音乐唱片一样
    -   仅允许原版声音事件

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:record": {
    "comparator_signal": 1,
    "duration": 5,
    "sound_event": "ambient.tame"
}
```

#### 声音事件

[此处](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/itemreference/examples/itemcomponents/minecraft_record?view=minecraft-bedrock-stable)列出了可用的声音

### 修复

修复物品组件：确定哪些物品可以用于修复定义的物品，以及指定物品将修复多少耐久。在格式版本1.20.10中从实验中发布。

默认情况下，它可以修复自身。它将结合两个耐久。

类型：对象

-   `repair_items`: 数组 - 修复物品条目列表。
    -   `repair_amount`: 整数/字符串
        -   修复的耐久量
        -   字符串类型中可以使用Molang。`math.random`可以使用。使用`context.other`获取附魔台的第二个插槽。
    -   `items`: 数组
        -   用于修复物品的物品
        -   必填字段

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:repairable":{
    "repair_items": [
        {
            "items":[
                "minecraft:diamond"
            ],
            "repair_amount": 10
        }
    ]
}
```

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:repairable":{
    "repair_items": [
        {
            "items":[
                "minecraft:diamond"
            ],
            "repair_amount": "math.random(1,10)"
        }
    ]
}
```

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:repairable":{
    "repair_items": [
        {
            "items":[
                "minecraft:diamond"
            ],
            "repair_amount": "math.min(q.remaining_durability + c.other->q.remaining_durability + math.floor(q.max_durability /20), c.other->q.max_durability)" // Vanilla formula
        }
    ]
}
```

### 射手

射手物品组件。必须具有`minecraft:use_modifiers`组件才能正常工作。在格式版本1.20.10中从实验中发布。

类型：对象

-   `ammunition`: 数组
    -   `item`: 字符串 - 表示物品描述符标识符。物品必须具有`minecraft:projectile`组件。
    -   `use_offhand`: 布尔值 - 当设置为`true`时，弹药可以从副手使用。
    -   `search_inventory`: 布尔值 - 确定是否可以搜索库存以使用弹药。如果在生存中使用，则必须为`true`。在创造中，如果`use_in_creative`为`false`，则必须为`true`。如果在创造中，则不会消耗弹药。
    -   `use_in_creative`: 布尔值 - 确定弹药是否可以在创造模式中使用。
-   `charge_on_draw`: 布尔值
    -   设置物品在拉弓时是否充能
    -   项目的`minecraft:use_modifiers` -> `use_duration`必须>=`max_draw_duration`。
-   `max_draw_duration`: 浮点数
    -   确定武器可以拉弓多长时间才会自动释放
-   `scale_power_by_draw_duration`: 布尔值
    -   当设置为`true`时，武器拉弓越久，释放时的威力越大

#### 弹药

设置用作弹药的实体。优先使用物品的顺序如下：首先，它会检查副手插槽是否匹配任何弹药。如果匹配，它会检查该对象是否将`use_offhand`设置为`true`。如果没有物品匹配副手，它将按顺序遍历数组，无论库存中的顺序如何。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:shooter": {
    "ammunition": [
        {
            "item": "custom_projectile",
            "use_offhand": true,
            "search_inventory": true,
            "use_in_creative": true
        }
    ],
    "max_draw_duration": 1.0,
    "scale_power_by_draw_duration": true,
    "charge_on_draw": false
}
```

### 应该消失

确定物品在世界中漂浮时是否最终消失。

类型：布尔值

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:should_despawn": true
```

### 通过数据堆叠

确定具有不同辅助值的相同物品是否可以堆叠。此外，定义物品实体在世界中漂浮时是否可以合并。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:stacked_by_data": true
```

### 存储物品

允许物品充当容器并存储其他物品。
该物品必须具有最大堆叠大小为1才能使此组件生效。

_从格式版本1.21.40及更高版本的`Bundles`实验中发布。_

类型：对象

-   `allow_nested_storage_items`: 布尔值
    -   确定其他存储物品是否可以放入容器中。
-   `allowed_items`: 数组
    -   定义容器中唯一允许的物品。
    -   如果为空，则容器中允许所有物品。
-   `banned_items`: 数组
    -   定义容器中不允许的物品。
-   `max_slots`: 整数 (1-64)
    -   定义容器中的插槽数量。
-   `max_weight_limit`: 整数

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:storage_item": {
    "max_slots": 64,
    "allow_nested_storage_items": true,
    "banned_items": [
        "minecraft:shulker_box",
        "minecraft:undyed_shulker_box"
    ]
}
```

### 存储重量限制

定义存储物品容器中所有物品的最大允许总重量。
该物品必须具有`minecraft:storage_item`组件才能使此组件生效。

-   要计算物品的重量，将64除以其最大堆叠大小。
-   堆叠到64的物品每个重量为1，堆叠到16的物品每个重量为4，不可堆叠的物品每个重量为64。

类型：对象

-   `max_weight_limit`: 整数

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:storage_weight_limit": {
    "max_weight_limit": 64
}
```

### 存储重量修正

定义物品在另一个存储物品内时添加的额外重量。

-   值为0表示该物品不允许在另一个存储物品内。

类型：对象

-   `weight_in_storage_item`: 整数 (0-64)

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:storage_weight_modifier": {
    "weight_in_storage_item": 4
}
```

### 挥动持续时间

确定玩家在挖掘、攻击或使用物品时挥动动画的持续时间（以秒为单位）。

#### 浮点定义 {#swing-duration-float}

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:swing_duration": {
    "value": 1
}
```

### 挥动声音

确定玩家攻击时持有的物品触发的原版声音事件。

#### 对象定义 {#swing-sounds-object}

-   `attack_miss`: 字符串（可选）
    -   确定未击中实体或未造成伤害时触发的原版声音事件。
-   `attack_hit`: 字符串（可选）
    -   确定击中实体并造成非暴击伤害时触发的原版声音事件。
-   `attack_critical_hit`: 字符串（可选）
    -   确定击中实体并造成暴击伤害时触发的原版声音事件。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:swing_sounds": {
    "attack_miss": "item.wooden_spear.attack_miss",
    "attack_hit": "item.wooden_spear.attack_hit"
}
```

### 标签

`tags`组件确定附加到物品的标签。

类型：对象

-   `tags`: 数组
    -   附加到物品的标签列表。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:tags": {
    "tags": [
        "custom_tag"
    ]
}
```

### 可投掷

可投掷物品组件。可投掷物品，例如雪球。在格式版本1.20.10中从实验中发布。物品必须具有`minecraft:projectile`组件。

类型：对象

-   `do_swing_animation`: 布尔值
    -   是否在投掷时使用挥动动画。
-   `launch_power_scale`: 浮点数
    -   投掷力量增加的缩放比例。
    -   可以为负。负值将使弹射物朝相反方向发射。
-   `max_draw_duration`: 浮点数
    -   可投掷物品的最大拉弓持续时间。
    -   可以为负。将立即发射。
    -   从测试中得知，如果最大值小于最小值，则没有副作用。
-   `max_launch_power`: 浮点数
    -   可投掷物品的最大发射力量。
    -   可以为负。
-   `min_draw_duration`: 浮点数
    -   可投掷物品的最小拉弓持续时间。
    -   可以为负。将立即发射。
-   `scale_power_by_draw_duration`: 布尔值
    -   投掷力量是否随拉弓时间增加。当`true`时，拉得越久，释放时的力量越大。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:throwable": {
    "do_swing_animation": false,
    "launch_power_scale": 1.0,
    "max_draw_duration": 0.0,
    "max_launch_power": 1.0,
    "min_draw_duration": 0.0,
    "scale_power_by_draw_duration": false
}
```

### 使用动画

确定使用物品时播放的动画。

类型：字符串

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:use_animation": "eat"
```

#### 使用动画

| 动画名称 |
| -------------- |
| `eat` |
| `drink` |
| `bow` |
| `block` |
| `camera` |
| `crossbow` |
| `none` |
| `brush` |
| `spear` |
| `spyglass` |

### 使用修饰符

修改使用效果，包括物品使用所需的时间以及与组件（如射手、可投掷或食物）结合使用时玩家的速度。

类型：对象

-   `emit_vibrations`: 布尔值（可选）
    -   确定物品开始和停止使用时是否发出振动。
-   `movement_modifier`: 浮点数 (`0.0-1.0`{lang=js})（可选）
    -   玩家在使用物品时速度的缩放值。
-   `start_sound`: 字符串（可选）
    -   确定物品开始使用时触发的原版声音事件。
-   `use_duration`: 浮点数
    -   物品使用所需的时间（以秒为单位）。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:use_modifiers": {
    "movement_modifier": 0.5,
    "use_duration": 1.0
}
```

### 可穿戴

确定物品可以装备的位置。如果选择了任何非手部插槽，则最大堆叠大小设置为1。

类型：对象

-   `slot`: 字符串
-   `protection`: 整数
-   `hides_player_location`: 布尔值
    -   确定佩戴物品的玩家是否会被定位栏和定位地图隐藏。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:wearable": {
    "slot": "slot.armor.chest",
    "protection": 10
}
```

#### 可穿戴插槽

| 插槽名称 |
| --------------------- |
| `slot.weapon.offhand` |
| `slot.armor.head` |
| `slot.armor.chest` |
| `slot.armor.legs` |
| `slot.armor.feet` |